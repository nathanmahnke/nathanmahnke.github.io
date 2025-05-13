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

    ---
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
    ---

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


You can also put regular text between your rows of images, even citations {% cite einstein1950meaning %}.
Say you wanted to write a bit about your project before you posted the rest of the images.
You describe how you toiled, sweated, _bled_ for your project, and then... you reveal its glory in the next row of images.

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
