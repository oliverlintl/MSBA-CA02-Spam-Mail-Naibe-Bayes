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
  all_words=[]    #create empty list
  emails = [os.path.join(root_dir, f) for f in os.listdir(root_dir)]     # create a list with email paths 
  for mail in emails:    # loop through all emails in the list of emails 
    with open(mail) as m:    # open emails 
        for i, line in enumerate(m):    # loop through each line
            if i == 2:     # start at the 3rd line which is text
                words = line.split()    # split the sentence into words 
                all_words += words    #append each word tolist
  dictionary = Counter(all_words)    # Counter count how many time each word appears in a list and create dictionary

  list_to_remove = list(dictionary)    # creating a list of keys/words without the count
  
  for item in list_to_remove:
    if item.isalpha() == False:    # .isalpha() check if all characters are letters
      del dictionary[item]    # remove all non letter characters
    elif len(item) == 1:    # check if any words are one-letter word
      del dictionary[item]    # remove all 1 letter words
  dictionary = dictionary.most_common(3000)    # .most_common() is a method to the Counter dictionary subclass
  return dictionary    # return a list of tuples 
```
> This function creates a dicitonary containing 3000 most common words from the training dataset. It also removes any non-character strings and single-character word or numeric.
> <br>
> **Second Function**:
```ruby
def extract_features(mail_dir):
    files = [os.path.join(mail_dir, fi) for fi in os.listdir(mail_dir)]    # get file path
    features_matrix = np.zeros((len(files), 3000))    # creating an empty matrix
    train_labels = np.zeros(len(files))
    count = 1;  
    docID = 0;  
    for fil in files:    # for path in file_paths, looping through each files from folder
        with open(fil) as fi:
            for i, line in enumerate(fi):    # loop through each line
                if i == 2:    # start at the 3rd line which is text
                    words = line.split()    # split on white space
                    for word in words:    # loop through each word in a sentence
                        wordID = 0
                        for i, d in enumerate(dictionary):    # d is the tuples in the list of tuples
                            if d[0] == word:    # d[0] is the word in the tuple, d[1] is the count of that word
                                wordID = i    # if there is a match of the word, i would be the column number of the feature_matrix
                                features_matrix[docID, wordID] = words.count(word)   # filling the matrix with the count of each word in a mail
            train_labels[docID] = 0;  # assume every sentence is non-spam which is labeled by 0   
            filepathTokens = fil.split('\\')    # split the file path in to seperate strings into a list. The string is actually seperated by '\\' nor '/'
            lastToken = filepathTokens[-1]    # choose the last item
            if lastToken.startswith('spmsg'):    # all spam mesages starts with spmsg
                train_labels[docID] = 1;    # spam messages are labeled as 1 
                count = count + 1    # add 1 to count when there is a match
            docID = docID + 1    # move on to the next message and add 1 to docID
    return features_matrix, train_labels
```
> This function extracts and populates the feature matrix, as well as creating a label column and analyze the file name to labeling the mail as spam or non-spam. 
> <br>
