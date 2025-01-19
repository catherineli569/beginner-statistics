import statistics

#get user input for data
def data_input():
  #determine if user input is number
  def is_valid_number(value):
      try:
          float(value)
          return True
      except ValueError:
          return False

  #get user input for data
  data = input("Enter data values separated by spaces or commas: ")

  #create list with user input for data
  data_list = []
  #reformat user input without commas
  for value in data.replace(",", " ").split():
      #adds valid user inputs to list
      if is_valid_number(value):
          data_list.append(float(value))

  #error message
  if not data_list:
      print("Invalid input. Please enter data values separated by spaces or commas")
      return data_input()

  #error message
  if len(data_list) < 3:
      print("Invalid input. Please enter at least 3 data values.")
      return data_input()
  elif len(data_list) > 20:
      print("Invalid input. Please enter no more than 20 data values.")
      return data_input()
  else:
      return data_list

#get user input for highest/ lowest suspected outlier
def suspected_outlier(data_list):
  #get user input for highest/ lowest suspected outlier
  outlier_input = input("Would you like to test the highest or lowest value: ").lower()

  #returns highest/ lowest suspected outlier
  if outlier_input == "highest":
      return max(data_list)
  elif outlier_input == "lowest":
      return min(data_list)
  #error message
  else:
      print("Invalid input. Please enter highest or lowest.")
      return suspected_outlier(data_list)

#calculate gcalc of suspected outlier in data
def gcalc(data_list, outlier_value):
   mean = statistics.mean(data_list)
   st_dev = statistics.stdev(data_list)
   return abs((outlier_value - mean) / st_dev)

#get gcrit from number of values in data
def gcrit(data_list):
  #dictionary of gcrit values
  gcrit_table = {"3": 1.153,
                 "4": 1.463,
                 "5": 1.672,
                 "6": 1.822,
                 "7": 1.938,
                 "8": 2.032,
                 "9": 2.110,
                 "10": 2.234,
                 "11": 2.285,
                 "12": 2.285,
                 "13": 2.331,
                 "14": 2.371,
                 "15": 2.409,
                 "16": 2.443,
                 "17": 2.475,
                 "18": 2.504,
                 "19": 2.532,
                 "20": 2.557}

  #calculate length of data list
  obs = str(len(data_list))

  #returns gcrit value
  if obs in gcrit_table:
      return gcrit_table[obs]
  #error message
  else:
      print("Invalid input.")
      return None

#use grubbs test to evaluate suspected outlier
#compare gcalc and gcrit
def grubbs_test(data_list, outlier_value):
  gcalc_value = gcalc(data_list, outlier_value)
  gcrit_value = gcrit(data_list)

  #error message
  if gcrit_value is None:
      print("Invalid data set. Data set is either too large or too small.")
      return False

  #prints gcalc and gcrit value
  print()
  print(f"Gcalc: {gcalc_value:0.3f}")
  print(f"Gcrit: {gcrit_value:0.3f}")

  #prints "is outlier"/ "is not outlier" message
  if gcalc_value > gcrit_value:
      print(f"{outlier_value} is an statistical outlier and should be removed from the data set.")
      return True
  else:
      print(f"{outlier_value} is not an statistical outlier and should not be removed from the data set.")
      return False

#removes outlier from data
#recalculates mean and st.dev if outlier
def main():
   data_list = data_input()
   outlier_value = suspected_outlier(data_list)
   grubbs_test_value = grubbs_test(data_list, outlier_value)

   if grubbs_test_value:
       #remove outlier (grubbs test)
       data_list.remove(outlier_value)
       print()
       #prints new mean and st. dev
       print(f"The new mean is {statistics.mean(data_list):0.3f}")
       print(f"The new standard deviation is {statistics.stdev(data_list):0.3f}")
   #exit program
   exit()

#runs program
if __name__ == "__main__":
  main()
