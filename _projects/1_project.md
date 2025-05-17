---
layout: page
title: MailGuard
description: Email Classification Recurrent Neural Network
img: assets/img/MailGuard.jpg
importance: 1
category: Personal
---
The original MailGuard was only designed to show an ability to generate a model, not to generate a working model. For this reason, MailGuard has been remastered for its publication here.

MailGuard was designed to classify a given email as spam (illegitimate) or ham (legitimate). Training a model contains many steps, which I'll outline below. 

Firstly, there's data to worry about. A model is useless without valuable data. This model begins with a CSV (Comma separated Values) file that contains roughly six thousand emails. Of those emails, about 80% are considered spam while only 20% is legitimate. Balancing this inequity in the training data between ham and spam, will take some work later. For now, the data needs to be parsed into something easier for the model to understand. 

The very first proccess done to the input dataset is to set all of it to lowercase as well as to remove any and all punctuation and numeric characters. Once the data only contains words, a process can be used to help make those words more computer-readable.

Forgive the personification of the computer for a moment. It's usefull in this instance to think about what a computer "sees". Words like "running," "runs," and "ran" all stem from the root word "run". Using a process called stemming, words in the input dataset can be trimmed down to their root form. This helps the computer to treat all those versions as the same word, making it easier to understand what a piece of text is really about.
So, instead of seeing:
- "He was running late."
- "She runs every morning."
The computer "sees":
- "He wa run late."
- "She run everi morn."

This is taken a step further when the punction, numeric characters, and stopwords are removed. Stopwords may seem like an unfamiliar concept, but most people find themselves using them without knowing. Stopwords are words like, "the, is, at, and, was, she, he, etc." which help to form proper sentences when speaking or writing but don't carry much meaning on their own.
Let's take the sentence:
> "She is running to the park every morning."
>
If stopwords are removed(she, is, to, the, every), the result is:
> "running park morning"
>
That still conveys the main idea:
> Someone is running to a park in the morning.
>


While this makes the input data less human-readable, the preparation vastly increase the power of the model to identify key actions and subjects without being distracted by "filler". 

Here is the code block that prepares the the dataset for use in model training and verification:

```python
import csv
import os
import shutil
import re
import nltk
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize
from nltk.stem import PorterStemmer

# Download necessary NLTK datasets
try:
    nltk.data.find('tokenizers/stopwords')
except LookupError:
    nltk.download('stopwords')
try:
    nltk.data.find('tokenizers/punkt')
except LookupError:
    nltk.download('punkt')
try:
    nltk.data.find('tokenizers/punkt_tab')
except LookupError:
    nltk.download('punkt_tab')
# Initialize stemmer
stemmer = PorterStemmer()

def preprocess_text(text):
    # Lowercasing
    text = text.lower()

    # Remove punctuation and numbers
    text = re.sub(r'[^\w\s]', '', text)
    text = re.sub(r'\d+', '', text)

    # Remove stop words
    stop_words = set(stopwords.words('english'))
    words = word_tokenize(text)
    filtered_words = [word for word in words if word not in stop_words]

    # Apply stemming
    filtered_words = [stemmer.stem(word) for word in filtered_words]

    return " ".join(filtered_words)

# Read the CSV file
with open("spam_ham_dataset.csv", "r", encoding='ISO-8859-1') as file:
    reader = csv.reader(file)
    next(reader)  # Skip the header row if it exists

    # Process each row in the CSV file
    for index, row in enumerate(reader, 1):
        try:
            content, label = row[:2]  # Extract only the first two values
        except ValueError:
            print(f"Invalid row {index} with values: {row}")
            continue
        # Determine the directory based on the label
        if label == "0":
            directory = "Ham"
        elif label == "1":
            directory = "Spam"
        else:
            print(f"Invalid label in row {index}: {label}")
            continue
        # Preprocess the email content
        content = preprocess_text(content)
        # Create the file in the corresponding directory
        filename = os.path.join(directory, f"{index}.txt")
        with open(filename, "w") as output_file:
            output_file.write(content)

print("Files created successfully.")
```

MailGuard’s model‐generation script picks up once all emails have been cleaned and organized into “Ham” and “Spam” text files. First, it reads every file from those two directories, tagging each message as legitimate (ham) or illegitimate (spam). To ensure the training process sees a balanced mix, the combined dataset, now several thousand labeled examples, is shuffled at random. This mixing helps prevent the model from learning any accidental ordering or clustering in the source files.

Next, the shuffled messages are split into two groups: one for teaching the model and one for testing its skills afterward. Eighty percent of the data goes into the training set, and the remaining twenty percent is held back to see how well the model performs on messages it has never seen. By using a fixed random seed, MailGuard ensures that this split is reproducible, so experiments can be repeated and compared reliably.

Since neural networks cannot work directly with plain text, MailGuard uses a text‐to‐numbers step called tokenization. A special Keras Tokenizer builds a vocabulary from the training messages, assigning each unique word its own integer. Any words that weren’t in that vocabulary are mapped to a special <UNK> token, ensuring the model can handle new or rare words at inference time. All messages, both in the training and test sets, are then converted into these integer sequences. To feed them into the network in batches, every sequence is padded (with zeros at the front) so that they all share the same length. The length to which the sequences are padded is the length of the longest input in the training set. This value is saved to a file, guaranteeing that future predictions use the exact same padding scheme.

With the data numerically encoded, MailGuard turns to the challenge of class imbalance. Because there are roughly four times more spam messages than ham, it computes class weights that tell the training algorithm to pay proportionally more attention to the underrepresented ham examples. This weighting discourages the model from simply predicting “spam” all the time, which would otherwise yield deceptively high accuracy on an imbalanced dataset.

The core of MailGuard is a straightforward but powerful Sequential neural network. It starts with an Embedding layer that projects each token into a 128‑dimensional vector space, giving the model rich representations of word meanings. An LSTM layer follows, scanning through the sequence of embeddings to capture context and word order, with a Dropout layer to randomly silence 30% of its outputs and reduce overfitting. Finally, a single‐unit Dense layer with a sigmoid activation outputs a probability between 0 and 1 for each message, representing its spam likelihood.

Training proceeds for up to ten passes through the data (epochs), using the Adam optimizer and binary cross‐entropy loss. A portion of the training set is reserved for validation, and an EarlyStopping mechanism watches the validation loss. If it doesn’t improve for five consecutive epochs, the training halts and reverts to the best recorded weights. During training, MailGuard tracks not only accuracy but also precision and recall, metrics that reveal how well the model balances false positives and false negatives on an imbalanced data mix.

Once training concludes, MailGuard evaluates the model on the held‑out test set, printing loss, accuracy, precision, and recall so that the developer can see exactly how the classifier performs on truly unseen data. To make deployment straightforward, the script saves the trained model in Keras’s .keras format, pickles (saves) the fitted Tokenizer for consistent preprocessing, and preserves the maximum sequence length for future padding. Finally, two plots display the training and validation curves for accuracy and loss, and a detailed classification report breaks down precision, recall, and F1‑score for both ham and spam classes—providing a full picture of MailGuard’s effectiveness before it ever sees a real user’s email.

<div class="row">
    <div class="col-sm mt-2 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ModelAccuracyGraph.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-2 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ModelLossGraph.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The above graphs depict the model's accuracy and loss during each epoch.
</div>

Below is the code used to generate the MailGuard model.

```python
# Import necessary libraries
import os
import re
import numpy as np
import matplotlib.pyplot as plt
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense, Embedding, LSTM, Dropout
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences
from tensorflow.keras.callbacks import EarlyStopping
from tensorflow.keras.utils import plot_model
from sklearn.utils.class_weight import compute_class_weight
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report
import pickle  # Needed to save the tokenizer

# Function to load text data and assign labels based on directory name
def load_data(directory):
    texts = []
    labels = []
    for filename in os.listdir(directory):
        if not filename.endswith(".txt"):
            continue  # Skip non-text files
        with open(os.path.join(directory, filename), 'r', encoding='utf-8', errors='ignore') as file:
            text = file.read()
            texts.append(text)
            labels.append(0 if directory.lower() == 'ham' else 1)  # 0 for Ham, 1 for Spam
    return texts, labels

# Load text data from "Ham" and "Spam" folders
ham_texts, ham_labels = load_data('Ham')
spam_texts, spam_labels = load_data('Spam')

# Combine Ham and Spam texts and labels, and shuffle the combined dataset
all_texts = ham_texts + spam_texts
all_labels = ham_labels + spam_labels
data = list(zip(all_texts, all_labels))
np.random.shuffle(data)  # Shuffle data to mix ham and spam examples
all_texts, all_labels = zip(*data)  # Unzip into separate lists

# Split the dataset into training and testing sets (80/20 split)
train_texts, test_texts, train_labels, test_labels = train_test_split(all_texts, all_labels, test_size=0.2, random_state=42)

# Initialize a tokenizer to convert text to sequences; use <UNK> for unknown words
tokenizer = Tokenizer(oov_token="<UNK>")
tokenizer.fit_on_texts(train_texts)  # Fit only on training data to prevent data leakage
train_sequences = tokenizer.texts_to_sequences(train_texts)
test_sequences = tokenizer.texts_to_sequences(test_texts)

# Determine the maximum sequence length for padding
max_sequence_length = max([len(sequence) for sequence in train_sequences + test_sequences])

# Save the maximum sequence length to a file for future use
with open('max_sequence_length.txt', 'w') as f:
    f.write(str(max_sequence_length))

# Pad sequences to the same length using the max length
train_data = pad_sequences(train_sequences, maxlen=max_sequence_length)
test_data = pad_sequences(test_sequences, maxlen=max_sequence_length)

# Calculate class weights to address class imbalance in the dataset
class_weights = compute_class_weight('balanced', classes=np.unique(train_labels), y=train_labels)
class_weight_dict = {i: class_weights[i] for i in range(len(class_weights))}

# Build a Sequential neural network model
model_dropout = Sequential()
model_dropout.add(Embedding(len(tokenizer.word_index) + 1, 128, input_length=max_sequence_length))  # Word embeddings
model_dropout.add(LSTM(64))  # LSTM layer for sequence learning
model_dropout.add(Dropout(0.3))  # Dropout for regularization
model_dropout.add(Dense(1, activation='sigmoid'))  # Output layer for binary classification
model_dropout.compile(
    loss='binary_crossentropy',  # Binary classification loss
    optimizer='adam',            # Adam optimizer
    metrics=['accuracy', 'Precision', 'Recall']  # Track accuracy, precision, recall
)

# Configure early stopping to prevent overfitting
early_stopping = EarlyStopping(monitor='val_loss', patience=5, restore_best_weights=True)

# Train the model using training data, with validation and early stopping
history_dropout = model_dropout.fit(
    train_data,
    np.array(train_labels),
    epochs=10,
    batch_size=32,
    validation_split=0.2,  # Use 20% of training data for validation
    callbacks=[early_stopping],
    class_weight=class_weight_dict  # Use computed class weights
)

# Evaluate model performance on test data
loss_dropout, accuracy_dropout, precision_dropout, recall_dropout = model_dropout.evaluate(test_data, np.array(test_labels))
print(f"Test Loss: {loss_dropout}")
print(f"Test Accuracy: {accuracy_dropout}")
print(f"Test Precision: {precision_dropout}")
print(f"Test Recall: {recall_dropout}")

# Save the trained model for future use
model_dropout.save(f'HamSpamDropoutFinal.keras')

# Save the tokenizer to use when processing future input
with open('tokenizer.pickle', 'wb') as handle:
    pickle.dump(tokenizer, handle, protocol=pickle.HIGHEST_PROTOCOL)

# Plot training and validation accuracy over epochs
plt.plot(history_dropout.history['accuracy'])
plt.plot(history_dropout.history['val_accuracy'])
plt.title(f'Model Accuracy')
plt.ylabel('accuracy')
plt.xlabel('epoch')
plt.legend(['train', 'val'], loc='upper left')
plt.show()

# Plot training and validation loss over epochs
plt.plot(history_dropout.history['loss'])
plt.plot(history_dropout.history['val_loss'])
plt.title(f'Model Loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train', 'val'], loc='upper left')
plt.show()

# Generate predictions on test data
predictions = model_dropout.predict(test_data)
predictions = (predictions > 0.5).astype(int)  # Convert probabilities to binary predictions

# Print a classification report (precision, recall, f1-score)
print(classification_report(test_labels, predictions))
```