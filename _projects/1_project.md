---
layout: page
title: MailGuard
description: Email Classification Recurrent Neural Network
img: assets/img/12.jpg
importance: 1
category: work
related_publications: true
---

MailGuard was designed to classify a given email as spam (illegitimate) or ham (legitimate). Before it can do so, it needs a large dataset on which to train. 
The code below shows the handling of a csv file containing emails and their classification as either spam or ham. It separates the emails into their respective folders for use in training.

    ```Python3
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

            # Create the file in the corresponding directory
            filename = os.path.join(directory, f"{index}.txt")
            with open(filename, "w") as output_file:
                output_file.write(content)
    ```

Once the data has been pre-processed, it can be handed to the newly created model for training. But first, the data needs to be loaded into respective array objects. A function is defined for this process and then called on each of the types of email.

    ```py
    # Load data from files
    def load_data(directory):
        texts = []
        labels = []
        for filename in os.listdir(directory):
            if not filename.endswith(".txt"):
                continue
            with open(os.path.join(directory, filename), 'r', encoding='utf-8', errors='ignore') as file:
                text = file.read()
                texts.append(text)
                labels.append(0 if directory.lower() == 'ham' else 1)  # 0 for Ham, 1 for Spam
        return texts, labels

    # Load Ham and Spam data
    ham_texts, ham_labels = load_data('Ham')
    spam_texts, spam_labels = load_data('Spam')
    ```

Those arraylists that have just been created are then then shuffled. This helps prevent the model from learning patterns based on the order of the data (e.g., all ham first, then all spam).

    ```Py
    # Concatenate and shuffle the data
    all_texts = ham_texts + spam_texts
    all_labels = ham_labels + spam_labels
    data = list(zip(all_texts, all_labels))
    np.random.shuffle(data)
    all_texts, all_labels = zip(*data)
    ```

The data is split:

- 80% will be used to teach the model (training set).
- 20% will be used to check how well it learned (test set).



    ```Python
    # Split the data into train and test sets
    train_texts, test_texts, train_labels, test_labels = train_test_split(all_texts, all_labels, test_size=0.2, random_state=42)
    ```



Neural networks can’t understand plain text, so:

- The tokenizer breaks each email into individual words.
- Each word is assigned a number.
- Emails are turned into lists of those numbers.

For the emails in their tokenized form to be useful, they all need to be the same size. So, they are padded to match the size of the largest email.

    ```python
    # Tokenize the texts and convert them to sequences
    tokenizer = Tokenizer()
    tokenizer.fit_on_texts(train_texts)
    train_sequences = tokenizer.texts_to_sequences(train_texts)
    test_sequences = tokenizer.texts_to_sequences(test_texts)
    
    # Pad the sequences to have the same length
    max_sequence_length = max([len(sequence) for sequence in train_sequences + test_sequences])
    train_data = pad_sequences(train_sequences, maxlen=max_sequence_length)
    test_data = pad_sequences(test_sequences, maxlen=max_sequence_length)
    ```
    

Now that the dataset has been fully processed and is ready for use, the model needs to be created. 
The model used in this project is a recurrent neural network that:

- Turns word numbers into dense word "meanings" (Embedding layer).
- Learns patterns in word sequences (LSTM layer).
- Prevents overfitting by randomly "turning off" parts of the network during training (Dropout).
- Outputs a single number between 0 and 1 (Dense layer with sigmoid) to classify emails as spam or ham.


    {% raw %}

    ```
    # Tokenize the texts and convert them to sequences
    tokenizer = Tokenizer()
    tokenizer.fit_on_texts(train_texts)
    train_sequences = tokenizer.texts_to_sequences(train_texts)
    test_sequences = tokenizer.texts_to_sequences(test_texts)
    
    # Pad the sequences to have the same length
    max_sequence_length = max([len(sequence) for sequence in train_sequences + test_sequences])
    train_data = pad_sequences(train_sequences, maxlen=max_sequence_length)
    test_data = pad_sequences(test_sequences, maxlen=max_sequence_length)
    ```

    {% endraw %}

    ```
    # Tokenize the texts and convert them to sequences
    tokenizer = Tokenizer()
    tokenizer.fit_on_texts(train_texts)
    train_sequences = tokenizer.texts_to_sequences(train_texts)
    test_sequences = tokenizer.texts_to_sequences(test_texts)
    
    # Pad the sequences to have the same length
    max_sequence_length = max([len(sequence) for sequence in train_sequences + test_sequences])
    train_data = pad_sequences(train_sequences, maxlen=max_sequence_length)
    test_data = pad_sequences(test_sequences, maxlen=max_sequence_length)
    ```



    {% raw %}

    ```python
    
    ```

    {% endraw %}
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


<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    You can also have artistically styled 2/3 + 1/3 images, like these.
</div>

The code is simple.
Just wrap your images with `<div class="col-sm">` and place them inside `<div class="row">` (read more about the <a href="https://getbootstrap.com/docs/4.4/layout/grid/">Bootstrap Grid</a> system).
To make images responsive, add `img-fluid` class to each; for rounded corners and shadows use `rounded` and `z-depth-1` classes.
Here's the code for the last row of images above:

{% raw %}

```html
<div class="row justify-content-sm-center">
  <div class="col-sm-8 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
```

{% endraw %}
