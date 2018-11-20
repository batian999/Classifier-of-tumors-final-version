# Classifier-of-tumors-final-version
# Fuchun Yang 
# 11/15/2018
###############################################################################
# Overall Tasks
# 1 - Create a training set
# 2 - Train a 'dumb' rule-based classifier
# 3 - Create a test set
# 4 - Apply rule-based classifier to test set
# 5 - Report accuracy of classifier
###############################################################################

###############################################################################
# CONSTANTS
# For use as dictionary keys in training/testing sets and computation of averages
# DONE - Do not modify.
###############################################################################
attributeList = []
attributeList.append("ID")
attributeList.append("radius")
attributeList.append("texture")
attributeList.append("perimeter")
attributeList.append("area")
attributeList.append("smoothness")
attributeList.append("compactness")
attributeList.append("concavity")
attributeList.append("concave")
attributeList.append("symmetry")
attributeList.append("fractal")
attributeList.append("class")

###############################################################################
# 1. Create a training set
# - Read in file
# - Create a dictionary for each line
# - Add this dictionary to a list
#
# makeTrainingSet
# parameters: 
#     - filename: name of the data file containing the training data records
#
# returns: trainingSet: a list of training records (each record is a dict,
#                       that contains attribute values for that record.)
###############################################################################
def makeTrainingSet(filename):
    # DONE - Do not modify.
    thisTrainingSet = []
    # Read in file
    for line in open(filename,'r'):
        if '#' in line:
            continue
        line = line.strip('\n')
        linelist = line.split(',')
        # Create a dictionary for the line
        #   assigns each attribute of the record (each item in the linelist)
        #   to an element of the dictionary, using the constant keys given
        #   in attributeList
        record = {}
        for i in range(len(attributeList)):
              if(i==11): #class label is a character, not a float
                  record[attributeList[i]] = linelist[31]() 
              else:
                  record[attributeList[i]] = float(linelist[i])
        # Add the dictionary to a list
        thisTrainingSet.append(record)        

    return thisTrainingSet

###############################################################################
# 2. Train 'Dumb' Classifier
# trainClassifier
# parameters:
#     - trainingSet: a list of training records (each record is a dict,
#                     that contains attribute values for that record.)
#
# returns: a dictionary of midpoints between the averages of each attribute's
#           values for benign and malignant tumors
###############################################################################
def trainClassifier(thisSet):

# This is to make an empty classifier dictionary and add things into it later. 
    thisClassifier = {}
# This is to make an empty list of 0s that is used later to add up the numbers for each attribute in each dictionary in the whole string of dictionaries. 
    total = [0]*len(thisSet[0])
# This is to reset a variable named count for calculating how many dictionaries there are in the string. 
    count = 0
    
# This is to enter into the dictionaries in the string. 
    for dic in thisSet: # Two dicts separately 
# This is to reset index at 0 for a new dictionary when the last dictionary's numbers are already in the lis. 
        index = 0
# This is to add up how many dictionaries are in the string. 
        count = count + 1
        
# This is to check the attributes in the dictionary. 
        for key in dic:
# If key is not "class" or "ID", convert the numbers of attributes to float numbers and add number that have same index in the dictionary together. 
            if key != "class" and key!="ID":
                total[index] = total[index] + float(dic[key])
# This is to calculate the mean value. 
                thisClassifier[key] = total[index]/count
# Since there are so many attributes in the dictionary, we must make the index iterate between the attributes. 
                index+=1
    
    return thisClassifier

###############################################################################
# 3. Create a test set
# - Read in file
# - Create a dictionary for each line
# - Initialize each record's predicted class to '0'
# - Add this dictionary to a list
#
# makeTestSet
# parameters: 
#     - filename: name of the data file containing the test data records
#
# returns: testSet: a list of test records (each record is a dictionary
#                       that contains attribute values for that record
#                       and where the predicted class is set to 0. 
###############################################################################
def makeTestSet(filename):

    # DONE - Do not modify.
    testset = makeTrainingSet(filename)

    for record in testset:
        record["predicted"] = 0

    return testset


###############################################################################
# 4. Classify test set
#
# classifyTestRecords
# parameters:
#      - testSet: a list of records in the test set, where each record
#                 is a dictionary containing values for each attribute
#      - classifier: a dictionary of midpoint values for each attribute
#
# returns: testSet with the predicted class set to either benign or malignant
#
# for each record, if the majority of attributes are greater than midpoint (which is
# stored in the classifer) then predict the record as malignant
###############################################################################
def classifyTestRecords(thisSet, thisClassifier):
# This string is to store the number of maglinant attributes in the whole dictionary in different dictionaries. 
    store_maglinant_attribute = []
    
# This is to enter into the dictionaries in the string.
    for dictionary in thisSet: # Two dicts separately 
# This is to reset the number of maglinat attributes in one new dictionary. 
        maglinant_attribute = 0
        
        for key in dictionary: 
# If attribute is not "ID" or "class" or "predicted", check to see if the value for one attribute is greater than 
# the mean value of that attribute in the classifier, if so, the maglinant attribute will add 1.
            if key != "ID" and key != "class" and key != "predicted":
                if float(dictionary[key]) > float(thisClassifier[key]):
                    maglinant_attribute = maglinant_attribute+1
# append the numbers in the list. 
        store_maglinant_attribute.append(maglinant_attribute)
        
# This is a for loop that checks index of numbers in the list. 
    for num in range(len(store_maglinant_attribute)):
# If the number of maglinant attributes is greater than 4, then change it to "M": maglinant,
# if not, change it to "B": benign.
        if store_maglinant_attribute[num] > 4:
            store_maglinant_attribute[num] = "M"
        else:
            store_maglinant_attribute[num] = "B"    

# This is to put the "M"or "B" into the "predicted" attribute one by one. 
    num = 0
    for record in thisSet:
        record['predicted'] = store_maglinant_attribute[num]
        num = num+1
        
    return thisSet


###############################################################################
# 5. Report Accuracy
# reportAccuracy
# parameters:
#      - testSet: a list of records in the test set, where each record
#                 is a dictionary containing values for each attribute
#                 and both the predicted and actual class values are set
#
# returns: None
#
# prints out the number correct / total and accuracy as a percentage
###############################################################################
def reportAccuracy(thisSet):
    
    truePositives = 0 #number of benign tumors called benign
    trueNegatives = 0 #number of malignant tumors called malignant
    falsePositives = 0 #number of benigns called malignant 
    falseNegatives = 0 #the number of malignants called benign

# This is to run through the record in thisset. 
    for record in thisSet:    #231 lines of data 
# This is to examine the situtations that when predicted = class. 
        if record['predicted'] == record['class']:
# one of the situation is that both of them two are "B", so truePositives will add one. 
            if record['predicted'] == 'B':
                truePositives = truePositives + 1
# the other one of the situation is that both of them two are "M", so trueNegatives will add one. 
            elif record['predicted'] == 'M':
                trueNegatives = trueNegatives + 1
# If the predicted is not equal to class
        else:
# If the predicted is M and class is B, falsePositives will add 1 
            if record['predicted'] == 'M':
                falsePositives = falsePositives + 1
# If the predicted is B and class is M, falseNegatives will add 1 

            elif record['predicted'] == 'B':
                falseNegatives = falseNegatives + 1
    
    print("There are ",truePositives,"benign tumors called benign.")
    print("There are ",trueNegatives,"malignant tumors called malignant.")
    print("There are ",falsePositives,"benigns called malignant.")
    print("There are ",falseNegatives,"malignants called benign.")
    correctPredictions = truePositives + trueNegatives
    incorrectPredictions = falsePositives + falseNegatives
    print("The total number of correct predictions is",correctPredictions,)
    print("The total number of incorrect predictions is",incorrectPredictions)

    return

###############################################################################
# main - starts the program
###############################################################################
def main():

    print ("Reading in training data...")       #This is task 1
# This is to initialize a string to store all the data. 
    trainingSet = []
    trainingFile = "cancerTrainingData.txt"
# This is the rough data without "predicted" value 
    trainingSet = makeTrainingSet(trainingFile)
    print ("Done reading training data.\n")
    print ("Training classifier...")                #This is task 2
# This is the mean values of the 10 attributes. 
    Classifier = trainClassifier(trainingSet)                                                         
    print ("Done training classifier.\n")

    print ("Reading in test data...")                   # This is task 3
    testFile = "cancerTestingData.txt"
# The testSet if rough data with "predicted"=0 attribute. 
    testSet = makeTestSet(testFile)
    print ("Done reading test data.\n")
    
    print ("Classifying records...") 
    
# this is to compare the testSet with the classifier and put the predicted values inside.                                                             
    finalTest = (classifyTestRecords(testSet, Classifier)) # This is Task 4
    print ("Done classifying.  Check accuracy.\n" )        # this is Task 5

# This is to call the function and report all the results. 
    reportAccuracy(finalTest)

    print ("Program finished.")
    
main()
