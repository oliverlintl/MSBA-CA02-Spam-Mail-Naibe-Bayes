# MSBA-CA02: Spam Mail Naive Bayes
MSBA Computer Assignment 02 - Spam Mail Detection by Naive Bayes
![alt text](Bees.jpg)

## Objective
> The objective of this exercise is to use Naive Bayes classification method to predict whether or not a mail is spam or non-spam. <br>
> And we will use a dictionary-based approach to train our Naive Bayes model. <br>

## Data
> The raw data is split into two folders for training and testing. The [training folder](train-mails) contains 702 messages, and the [test folder](test-mails) contains 260 messages. 

## Dictionary
> We use two functions to extract data and create what we need to train our model. <br>
> **First function**:
```ruby
def make_Dictionary(root_dir):
  all_words=[]
  emails = [os.path.join(root_dir, f) for f in os.listdir(root_dir)]
  for mail in emails:
    with open(mail) as m:
      for line in m:
        words = line.split()
        all_words += words
  dictionary = Counter(all_words)
  list_to_remove = list(dictionary)
  
  for item in list_to_remove:
    if item.isalpha() == False:    # .isalpha() check if all characters are letters
      del dictionary[item]    # remove all non letter characters
    elif len(item) == 1:
      del dictionary[item]    # remove all 1 letter words
  dictionary = dictionary.most_common(3000)
  return dictionary
```
> This function creates a dicitonary containing 3000 most common words from the training dataset. It also removes any non-character strings and single-character word or numeric.
> <br>
> **Second Function**:
```ruby
def extract_features(mail_dir):
    files = [os.path.join(mail_dir, fi) for fi in os.listdir(mail_dir)]
    features_matrix = np.zeros((len(files), 3000))
    train_labels = np.zeros(len(files))
    count = 1;  
    docID = 0;  
    for fil in files:
        with open(fil) as fi:
            for i, line in enumerate(fi):
                if i == 2:
                    words = line.split()
                    for word in words:
                        wordID = 0
                        for i, d in enumerate(dictionary):
                            if d[0] == word:
                                wordID = i
                                features_matrix[docID, wordID] = words.count(word)   # filling the matrix with the count of each word in a mail
            train_labels[docID] = 0;  
            filepathTokens = fil.split('/')
            lastToken = filepathTokens[len(filepathTokens) - 1]
            if lastToken.startswith('spmsg'):
                train_labels[docID] = 1;
                count = count + 1
            docID = docID + 1
    return features_matrix, train_labels
```
> This function extracts and populates the feature matrix, as well as creating a label column and analyze the file name to labeling the mail as spam or non-spam. 
> <br>
