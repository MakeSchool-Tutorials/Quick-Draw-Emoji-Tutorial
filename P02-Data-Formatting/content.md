---
title: "Data Formatting"
slug: data-formatting
---

It's very normal when working with Machine Learning that you will be using data sets that need to be re-formatted in some way to make it easier to train the model with it. This is especially true when you are using datasets collected by others, who may not have stored it in the ideal format or may have included other bits of data that you don't want to include.

Even in this project however, we need to do a little data formatting to ensure that our Machine Learning model performs well. So far we are storing raw data that pairs emoji labels (strings) with drawing features (2D arrays). The string emoji labels are already good to go, but we need to convert the 2D array into a 1D array.

> [info]
> - **1D Arrays** are the typical basic list, a single list of many objects: like `[0, 1, 2, 3, 4]` or `["hello", "world"]`
> - **2D Arrays** are lists that contain lists: `[[0, 1, 2], [3, 4, 5]]`
> 2D arrays are a common way of representing grid information, with the first indices representing the column, and the second representing the row:
>```python
>grid_array = [["Column 0, Row 0", "Column 0, Row 1"],["Column 1, Row 0", "Column 1, Row 1"]]
>print(grid_array[0][0]) // prints "Column 0, Row 0"
>print(grid_array[1][0]) // prints "Column 1, Row 0"
>```
>

We need to create a helper function that will "flatten" 2D arrays, to convert them so that all of the values are kept in order, but removed from the inner lists: from this `[[0, 1, 2], [3, 4, 5]]` to this `[0, 1, 2, 3, 4, 5]`

> [action]
> In `model.py`, find where it says `# TODO: add the flatten_list(list_2d) function here`, and on a new line underneath, add the following code:
>```python
>def flatten_list(list_2d):
>  flat_list = []
>  for sublist in list_2d:
>    for item in sublist:
>      flat_list.append(item)
>  return flat_list
>```
>

In the next step, we'll use this helper to reformat and save the data so that we can train a Machine Learning model with it.

## Check

We aren't using the helper officially yet, but we can still check that it's working by adding some print statements and checking the python console.

First, expand the console by expanding the bottom panel of the results window.

<!-- TODO: image of panel closed -->

<!-- TODO: image of panel open -->

> [action]
> In `model.py`, right below where you just added the `flatten_list(list_2d)` function, add the following code:
>```python
>example_list = [["a", "b", "c"],["d", "e", "f"]]
>print(example_list)
>print(flatten_list(example_list))
>```
>

In the console, you can click the "Clear" button (⌫) to erase previous data. Re-run the program, then scroll up to the top. You should see your print statements near the top, like this:
```
Python 3.7.4 (default, Jul  9 2019, 00:06:43)
[GCC 6.3.0 20170516] on linux
[['a', 'b', 'c'], ['d', 'e', 'f']]
['a', 'b', 'c', 'd', 'e', 'f']
 * Serving Flask app "app" (lazy loading)
 * Environment: production
```

Once you have confirmed that it is working correctly, you can comment out or remove `example_list` and the print statements in `model.py`.

<!-- TODO: image of panel with print statements -->

# Prepare Data for Model Training

Now that we can convert 2D to 1D lists, it's time to load the data from `samples.txt` and put it into two separate lists - one for the labels and one for the features.

> [action]
> In `model.py`, find `# TODO: add machine learning variables here`, and add the following new global variables beneath it:
>```python
>training_features = []
>training_labels = []
>```
>

We are making and using global variables so that we can use `load_training_data()` to format and save the current samples to them and have a separate function that is responsible for using the loaded training data to train the model.

> [action]
> In `model.py`, find `# TODO: add the load_training_data() function here`, and add the following code:
>```python
>def load_training_data():
>  global training_features, training_labels
>  training_features = []
>  training_labels = []
>  sample_counts = {}
>
>  text=open("samples.txt", "r")
>  lines = text.readlines()
>  for line in lines:
>    data = json.loads(line)
>    label = data["label"]
>    features = flatten_list(data["features"])
>    training_labels.append(label)
>    training_features.append(features)
>    sample_counts[label] = sample_counts.get(label, 0) + 1
>
>  return sample_counts
>```
>

This is our largest function so far, and it's doing several important steps:

1. It accesses the global variables `training_features` and `training_labels` and resets them (so that we don't duplicate data by forgetting to erase old data)
1. Then, it opens `sample.txt` in **read** mode (`r`) and uses a `for loop` to go over each line of the saved sample data.
1. For each line, it saves the emoji label to `training_labels` and flattens and then saves the grid array as a 1d feature list to `training_features`
1. It also creates and returns a dictionary (`sample_counts`) with information about the training data indicating how many training samples we have for each emoji. This info is then passed back to the webpage to update the display.

## Check

Until we complete the training step, we won't see the webpage update using the `sample_counts` information, but we can use another `print` statement to help us confirm that things are working correctly before we move on.

> [action]
> In `model.py`, where you just added the `load_training_data()` function, add `print(sample_counts)` on the line **before** the last line `return sample_counts`.

Ensure that you have some data in `samples.txt` and re-run the program. Look in the python console to see if the print statement shows the correct number of samples.

<!-- TODO: image of panel with print statements about sample counts -->

If you are satisfied that it's correct, you can remove or comment out the `print` statement.

Take a moment to check your code so far.

> [solution]
>
>```python
>### IMPORT HELPERS ###
>import json
>from sklearn import tree
>
>### CUSTOM DISPLAY VARIABLES ###
># TODO: add global display variables here
>created_by = "Jordan"
>learn_emoji = ["🙂"]
>
>### MACHINE LEARNING MODEL VARIABLES ###
># TODO: add machine learning variables here
>training_features = []
>training_labels = []
>
>### DATA COLLECTION ###
># TODO: add the save_sample(data) function here
>def save_sample(data):
>  if "label" in data.keys() and "features" in data.keys():
>    sample = {"label": data["label"], "features":data["features"]}
>    data_string = json.dumps(sample)
>    text = open("samples.txt","a+")
>    text.write(f'{data_string}\n')
>    text.close()
>    return True
>
># # TODO: add the delete_samples() function here
>def delete_samples():
>  open("samples.txt", "w").close()
>  return True
>
># ### DATA FORMATTING ###
># # TODO: add the load_training_data() function here
>def load_training_data():
>  global training_features, training_labels
>  training_features = []
>  training_labels = []
>  sample_counts = {}
>
>  text=open("samples.txt", "r")
>  lines = text.readlines()
>  for line in lines:
>    data = json.loads(line)
>    label = data["label"]
>    features = flatten_list(data["features"])
>    training_labels.append(label)
>    training_features.append(features)
>    sample_counts[label] = sample_counts.get(label, 0) + 1
>
>  # print(sample_counts)
>  return sample_counts
>
># # TODO: add the flatten_list(list_2d) function here
>def flatten_list(list_2d):
>  flat_list = []
>  for sublist in list_2d:
>    for item in sublist:
>      flat_list.append(item)
>  return flat_list
>
># example_list = [["a", "b", "c"],["d", "e", "f"]]
># print(example_list)
># print(flatten_list(example_list))
>
>#### MODEL TRAINING ###
># TODO: add the update_training() function here
>
>### MODEL PREDICTION ###
># TODO: add the predict(data) function here
>
>```
>
