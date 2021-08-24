# Modifying an Existing To-Do List Script and Adding Functions

**Dev:** *CMathews*
**Date:** *8.24.21*

## Introduction
The following document outlines the steps taken to modify and update a Python script to demonstrate Pickling and Structured Error Handling.

## Process
1.	Write the script in PyCharm: I modified Assignment06 in effort to understand the impacts of Pickling and how it differs from other processes. I could not identify any user errors that would not be addressed by the current code.
```
# ---------------------------------------------------------------------------- #
# Title: Assignment 07
# Description: Demonstrates Pickling (Saving Data in Binary Format)
#              and Structured Error Handling
# ChangeLog (Who,When,What):
# RRoot,1.1.2030,Created started script
# RRoot,1.1.2030,Added code to complete assignment 5
# CMathews,8.15.2021,Modified code to complete assignment 6
# CMathews,8.17.2021,Modified output formatting
# CMathews,8.23.2021,Modified code to complete assignment 7
# ---------------------------------------------------------------------------- #

import pickle

# Data ----------------------------------------------------------------------- #
# Declare variables and constants
strFileName = "ToDoList.dat"  # The name of the data file
objFile = None  # An object that represents a file
dicRow = {}  # A row of data separated into elements of a dictionary {Task,Priority}
lstTable = []  # A list that acts as a 'table' of rows
strChoice = ""  # Captures the user option selection
strTask = ""  # Captures the user task data
strPriority = ""  # Captures the user priority data
strStatus = ""  # Captures the status of processing functions


# Processing  ---------------------------------------------------------------- #
class Processor:
    """  Performs Processing tasks """

    @staticmethod
    def read_data_from_file(strFileName, lstTable):
        """ Reads data from a file into a list of dictionary rows

        :param file_name: (string) with name of file:
        :param list_of_rows: (list) you want filled with file data:
        :return: (list) of dictionary rows
        """
        lstTable.clear()
        try:
            objFile = open(strFileName, "rb")
            lstTable += pickle.load(objFile)
            objFile.close()
        except:
            objFile = open(strFileName, "wb")

    @staticmethod
    def add_data_to_list(task, priority, list_of_rows):
        """Adds a task and its priority to the list of dictionary rows

        :param task: (string) with name of task:
        :param priority: (string) with priority of task:
        :param list_of_rows: (list) data you want added to list
        :return: (list) of dictionary rows
        """
        dicRow = {"Task": task, "Priority": priority}
        list_of_rows.append(dicRow)
        return list_of_rows, '\nSuccess - the task has been added.'

    @staticmethod
    def remove_data_from_list(task, list_of_rows):
        """Removes a task and its priority from the list

        :param task: (string) of task to remove
        :param list_of_rows: (list) of dictionary rows
        :return: (list) of dictionary rows, (string) message
        """
        for item in list_of_rows:
            if task == item["Task"]:
                list_of_rows.remove(item)
                return list_of_rows, "\nSuccess - the task has been removed."
        else:
            return list_of_rows, '\nError - task not found.'

    @staticmethod
    def write_data_to_file(strFileName, list_of_rows):
        """Writes dictionary data to text file

        :param: strFileName: (object) of text file
        :param: list_of_rows: (list) of dictionary rows
        :return: (list) of dictionary rows, (string) message
        """
        objFile = open(strFileName, "wb")
        pickle.dump(list_of_rows, objFile)
        objFile.close()
        return list_of_rows, '\nSuccess - the data has been saved.'


# Presentation (Input/Output)  -------------------------------------------- #
class IO:
    """ Performs Input and Output tasks """

    @staticmethod
    def print_menu_tasks():
        """  Display a menu of choices to the user

        :return: nothing
        """
        print('''
        Menu of Options
        1) Add a new Task
        2) Remove an existing Task
        3) Save Data to File        
        4) Reload Data from File
        5) Exit Program
        ''')
        print()  # Add an extra line for looks

    @staticmethod
    def input_menu_choice():
        """ Gets the menu choice from a user

        :return: string
        """
        choice = str(input("Which option would you like to perform? [1 to 5] - ")).strip()
        print()  # Add an extra line for looks
        return choice

    @staticmethod
    def print_current_tasks_in_list(list_of_rows):
        """ Shows the current Tasks in the list of dictionaries rows

        :param list_of_rows: (list) of rows you want to display
        :return: nothing
        """
        print("\n******* Current Tasks: *******\n")
        for row in list_of_rows:
            print(row["Task"] + " (" + row["Priority"] + ")")
        print("\n******************************")
        print()  # Add an extra line for looks

    @staticmethod
    def input_yes_no_choice(message):
        """ Gets a yes or no choice from the user

        :return: string
        """
        return str(input(message)).strip().lower()

    @staticmethod
    def input_press_to_continue(optional_message=''):
        """ Pause program and show a message before continuing

        :param optional_message:  An optional message you want to display
        :return: nothing
        """
        print(optional_message)
        input('\nPress the [Enter] key to continue.')

    @staticmethod
    def input_new_task_and_priority():
        task = input("Task name: ")
        priority = input("Task priority: ")
        return task, priority

    @staticmethod
    def input_task_to_remove():
        task = input("What task would you like to remove? ")
        return task


# Main Body of Script  ------------------------------------------------------- #

# Step 1 - When the program starts, Load data from ToDoFile.dat.
Processor.read_data_from_file(strFileName, lstTable)  # read file data

# Step 2 - Display a menu of choices to the user
while True:
    # Step 3 Show current data
    IO.print_current_tasks_in_list(lstTable)  # Show current data in the list/table
    IO.print_menu_tasks()  # Shows menu
    strChoice = IO.input_menu_choice()  # Get menu option

    # Step 4 - Process user's menu choice
    if strChoice.strip() == '1':  # Add a new Task
        strTask, strPriority = IO.input_new_task_and_priority()
        lstTable, strStatus = Processor.add_data_to_list(strTask, strPriority, lstTable)
        IO.input_press_to_continue(strStatus)
        continue  # to show the menu

    elif strChoice == '2':  # Remove an existing Task
        strTask = IO.input_task_to_remove()
        lstTable, strStatus = Processor.remove_data_from_list(strTask, lstTable)
        IO.input_press_to_continue(strStatus)
        continue  # to show the menu

    elif strChoice == '3':  # Save Data to File
        strChoice = IO.input_yes_no_choice("Save this data to file? (Y/N) - ")
        if strChoice.lower() == "y":
            lstTable, strStatus = Processor.write_data_to_file(strFileName, lstTable)
            IO.input_press_to_continue(strStatus)
        else:
            IO.input_press_to_continue("Save Cancelled!")
        continue  # to show the menu

    elif strChoice == '4':  # Load File Data
        print("Warning: Unsaved Data Will Be Lost!")
        strChoice = IO.input_yes_no_choice("\nAre you sure you want to reload data from file? (Y/N) -  ")
        if strChoice.lower() == 'y':
            Processor.read_data_from_file(strFileName, lstTable)  # read file data
            print("\nData has been reloaded.")
            IO.input_press_to_continue()
        else:
            IO.input_press_to_continue("\nFile Reload Cancelled!")
        continue  # to show the menu

    elif strChoice == '5':  # Exit Program
        IO.input_press_to_continue()
        print("\nGoodbye!")
        break  # and Exit

    else:
        print("Please select from the available menu options.")
```

3.	Run the script in PyCharm (see pdf attachments).
4.	Run the script in the Command Prompt window.
    ![Image1](https://github.com/cmathews10/IntroToProg-Python-Mod07/blob/main/Picture1.png "Command Prompt Window")
    ![Image1](https://github.com/cmathews10/IntroToProg-Python-Mod07/blob/main/Picture2.png "Command Prompt Window")
    ![Image1](https://github.com/cmathews10/IntroToProg-Python-Mod07/blob/main/Picture3.png "Command Prompt Window")
    ![Image1](https://github.com/cmathews10/IntroToProg-Python-Mod07/blob/main/Picture4.png "Command Prompt Window")
    ![Image1](https://github.com/cmathews10/IntroToProg-Python-Mod07/blob/main/Picture5.png "Command Prompt Window")
5.  Confirm user input is being saved to the defined file location.
    ![Image1](https://github.com/cmathews10/IntroToProg-Python-Mod07/blob/main/Picture6.png "Command Prompt Window")

## Summary
This document outlined the steps taken to modify and update a Python script to demonstrate Pickling and Structured Error Handling.
