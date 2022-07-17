import time
import pandas as pd
import numpy as np

CITY_DATA = { 'Chicago': 'chicago.csv',
              'New York City': 'new_york_city.csv',
              'Washington': 'washington.csv' }
""" Defining 'dictionary key as city names' and 'values as respective csv file's' to
access them according to the user input """
Months_DATA = ['January', 'February', 'March', 'April', 'May', 'June']
Days_OF_WEEK = ['Sunday','Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']

def get_filters():

    while True:
      city = str(input("\nWhich city would you like to filter by? New York City,\n"
      "Chicago or Washington?\n")).title().strip()

      if city not in ('New York City', 'Chicago', 'Washington'):
         print("Sorry, your choice is not available.Please Try again.")
         continue
      else:
         break

    #  getting user input for month (None, January, February, ... , June)

    while True:
      month = str(input("\nWhat's your preferable way for filteration, [day], [month], [both], if month kindly choose one of them or choose none if you don't have any preference ? January, February, March, April, May, June or None'\n")).title().strip()
      if month not in ('January', 'February', 'March', 'April', 'May', 'June', 'None'):
         print("Kindly check your answer and Try again.")
         continue
      else:
         break
    # gettin' user input for day of week (None, Sun, Mon, Tue, ...)

    while True:
      day = str(input("\nAre you looking for a particular day? If so, kindly enter the day as follows: Sunday, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday or type 'None' if you do not have any preference.\n")).title().strip()
      if day not in ('Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'None'):
         print("Sorry, I didn't catch that. Try again.")
         continue
      else:
         break
    print('-'*90)
    return (city, month, day)

def load_data(city, month, day):

    """
    Loads data for the specified city and filters by month and day if applicable.
    Args:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    Returns:
        df - Pandas DataFrame containing city data filtered by month and day
    """
    # file data loading into datab.
    df = pd.read_csv(CITY_DATA[city])

    # converting Start Time column into datetime
    df['Start Time'] = pd.to_datetime(df['Start Time'])
    #  month and day of week from Start Time to create new columns
    df['month'] = df['Start Time'].dt.month_name()
    df['day_of_week'] = df['Start Time'].dt.day_name()


# filteration by month to create the new dataframe
    if month != "None":
        df = df[df['month'] == month]

    # filter by day of week if applicable to create the new dataframe
    if day != "None":
        df = df[df['day_of_week'] == day.title()]
    return df

def time_stats(df):
    """Displays statistics on the most frequent times of travel."""

    print('\nCalculating The Most Frequent Times of Travel...\n')
    start_time = time.time()

    # Displaying the most common month
    The_Pop_Month = df['month'].mode()[0]
    print('Most Common Month:', The_Pop_Month)

    # Displaying the most common day of week
    The_Pop_Day = df['day_of_week'].mode()[0]
    print('Most Common day:', The_Pop_Day)

    # Displaying the most common start hour
    df['hour'] = df['Start Time'].dt.hour
    The_Pop_Hour = df['hour'].mode()[0]
    print('Most Common Hour:', The_Pop_Hour)

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*90)

def station_stats(df):
    """Displays statistics on the most popular stations and trip."""

    print('\nCalculating The Most Popular Stations and Trip...\n')
    start_time = time.time()

    # Displaying the most commonly used start station
    St_Station = df['Start Station'].value_counts().idxmax()
    print('\nThe Most Commonly used Start Station:..', St_Station)

    # Displaying most commonly used end station
    End_Station = df['End Station'].value_counts().idxmax()
    print('\nThe Most Commonly used end station:', End_Station)

    # Displaying the most frequent combination of start station and end station trip
    Combination_Station = df.groupby(['Start Station', 'End Station']).count()
    print('\nMost Commonly used combination of start station and end station trip:', St_Station, " & ",         End_Station)

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*90)

def trip_duration_stats(df):
    """Displays statistics on the total and average trip duration."""

    print('\nCalculating Trip Duration...\n')
    start_time = time.time()

    # Displaying total travel time
    Total_Travel_Time = sum(df['Trip Duration'])
    print('Total travel time:', Total_Travel_Time/86400, " Days")

    # Displaying mean travel time
    Mean_Travel_Time = df['Trip Duration'].mean()
    print('Mean travel time:', Mean_Travel_Time/3660, " hour")

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*90)

def user_stats(df):
    """Displays statistics on bikeshare users."""

    print('\nCalculating User Stats...\n')
    start_time = time.time()

    # Displays counts of user types
    User_Type = df['User Type'].value_counts()
    print('Counts of users type \n', User_Type ,'\n')

    # Displays counts of gender
    if CITY_DATA != 'Washington':
        gender_count = df['Gender'].value_counts()
        print("\nGender Types: {}".format(gender_count))


    # Displays earliest, most recent, and most common year of birth

        max_birth_year = df['Birth Year'].max()
        print("\nThe Most Recent Birth Year is {} ".format(max_birth_year))

        Min_birth_year = df['Birth Year'].min()
        print("\nThe Most Earliest Birth Year is {}".format(Min_birth_year))

        Frequent_birth_year = df['Birth Year'].mode()[0]
        print("\nThe Most Frequent Birth Year is {} ".format(Frequent_birth_year))

 # handling exception if Birth Year data is unavailable
        if 'Birth Year' not in df.columns:
            print("\nUnfortunatly....! for {} your requested Birth Year data is not available ".format(city))

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*90)

def display_data(df) :

    repeat=input("do you want to see the first five raws of data? [yes],[no]\n").lower()
    while repeat != 'yes'and repeat !='no':
        print('there is an error in input !!!')
        repeat=input("do you want to see the first five raws of data? [yes],[no]\n").lower()

    while repeat=='yes':
         for i in range(10):
            if repeat=='yes':
                print(df.iloc[i*5:(i*5)+5,:])
                repeat=input("do you want to see the next five raws of data? [yes],[no]\n").lower()
                while repeat != 'yes'and repeat!='no':
                    print('ther is error in input !!!')
                    repeat=input("do you want to see the next five raws of data? [yes],[no]\n").lower()

            else:
                break

def main():
    while True:
        city, month, day = get_filters()
        df = load_data(city, month, day)

        time_stats(df)
        station_stats(df)
        trip_duration_stats(df)
        user_stats(df)
        display_data(df)

        restart = input('\nWould you like to restart? Enter yes or no.\n')
        if restart.lower() != 'yes':
            break


if __name__ == "__main__":
	main()
