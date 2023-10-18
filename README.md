# Restaurant-Management-System-
#include <stdio.h>

#include <stdlib.h>

#include <string.h>

#define MAX_DAYS_IN_MONTH 31

#define MAX_DATA_LENGTH 100

#define FILENAME "calendardemo_data1211.txt"

#define TOTAL_SALES_FILE "total_sales111.txt"

#define WEEKLY_AVERAGE_FILE "weekly_average22dem.txt"



// Structure to represent a calendar date

struct Date {

    int day;

    int month;

    int year;

};



// Structure to represent product sales

struct ProductSales {

    char productName[MAX_DATA_LENGTH];

    float totalSales;

    float weeklyAverage;

};



// Function to print a basic calendar for a given month

void printCalendar(int month, int year) {

    printf(" Calendar for %d/%d\n", month, year);

    printf("Sun Mon Tue Wed Thu Fri Sat\n");

    // Determine the day of the week for the 1st day of the month

    struct Date currentDate = {1, month, year};

    int dayOfWeek = 0; // 0: Sunday, 1: Monday, ..., 6: Saturday

    while (currentDate.day > 1) {

    currentDate.day--;

            dayOfWeek = (dayOfWeek - 1 + 7) % 7;



        }



        // Print spaces for days before the 1st day of the month

        for (int i = 0; i < dayOfWeek; i++) {

            printf(" ");

        }

        // Print days of the month

        for (int day = 1; day <= MAX_DAYS_IN_MONTH; day++) {

            printf("%3d ", day);

            dayOfWeek = (dayOfWeek + 1) % 7;



            // Start a new line at the end of the week

            if (dayOfWeek == 0) {

                printf("\n");

            }

        }



        printf("\n");



}



// Function to store data for a specific date in a daily file

void storeData(struct Date date, const char *data) {

    // Implement the logic to store data for a given date

    FILE *file = fopen(FILENAME, "a");   //append mode to read and write

    if (file == NULL) {

        perror("Error opening file");

        return;

    }



    // Store data in the format: mm/dd/yyyy: data

    fprintf(file, "%02d/%02d/%04d: %s\n", date.month, date.day, date.year, data);



    fclose(file);

}



// Function to print data for a specific date from a daily file

void printData(struct Date date) {

    // Implement the logic to print data for a given date

    FILE *file = fopen(FILENAME, "r");

    if (file == NULL) {

        perror("Error opening file");

        return;

    }



    char line[MAX_DATA_LENGTH];

    char targetDate[12]; // Format: mm/dd/yyyy

    snprintf(targetDate, sizeof(targetDate), "%02d/%02d/%04d", date.month, date.day, date.year);



    printf("Data for %s:\n", targetDate);



    while (fgets(line, sizeof(line), file)) {

        char currentDate[12];



        if (sscanf(line, "%[^:]: %[^\n]", currentDate, line) == 2) {

            if (strcmp(currentDate, targetDate) == 0) {

                printf("%s\n", line);

                

            }

        }

    }



    fclose(file);

}





// Function to calculate product sales data based on daily data

void calculateProductSales() {

    // Function to calculate product sales data

    

        FILE *file = fopen(FILENAME, "r");

        if (file == NULL) {

            perror("Error opening file");

            return;

        }



        // Initialize a data structure to keep track of product sales

        struct ProductSales productSales[5];

        strcpy(productSales[0].productName, "Vadapav");

        strcpy(productSales[1].productName, "Samoso");

        strcpy(productSales[2].productName, "Burger");

        strcpy(productSales[3].productName, "Pizza");

        strcpy(productSales[4].productName, "Manchurian");

        for (int i = 0; i < 5; i++) {

            productSales[i].weeklyAverage = 0.0;

        }



        char line[MAX_DATA_LENGTH];



        while (fgets(line, sizeof(line), file)) {

            struct Date currentDate;

            int quantity;

            float cost;



            if (sscanf(line, "%02d/%02d/%04d: %d units at Rs%f", &currentDate.month, &currentDate.day, &currentDate.year, &quantity, &cost) == 5) {

                // Determine the product based on the quantity

                int menuItem = -1;

                switch (quantity) {

                    case 10:

                        menuItem = 1; // Vadapav

                        break;

                    case 12:

                        menuItem = 2; // Samoso

                        break;

                    case 20:

                        menuItem = 3; // Burger

                        break;

                    case 25:

                        menuItem = 4; // Pizza

                        break;

                    case 30:

                        menuItem = 5; // Manchurian

                        break;

                }



                if (menuItem != -1) {

                    // Update the product sales data

                    productSales[menuItem - 1].weeklyAverage += cost;

                }

            }

        }



        // Close the file

        fclose(file);



        // Print the product sales data

        for (int i = 0; i < 5; i++) {

            printf("Product: %s - Weekly Average Sales: Rs%.2f\n", productSales[i].productName, productSales[i].weeklyAverage);

        }

    



    printf("Calculating product sales data...\n");

}



// Function to print the product with the most sales based on product sales data

void calculateAndStoreWeeklyAverage(int month, int year) {

    // Function to calculate and store the weekly average

    FILE *file = fopen(FILENAME, "r");

    if (file == NULL) {

        perror("Error opening file");

        return;

    }



    char line[MAX_DATA_LENGTH];

    float weeklyTotal = 0.0;

    int weekDayCount = 0;

    int weekNumber = 1;



    FILE *weeklyAverageFile = fopen(WEEKLY_AVERAGE_FILE, "w"); // Open for writing, creates the file if it doesn't exist

    if (weeklyAverageFile == NULL) {

        perror("Error opening weekly average file");

        fclose(file); // Close the data file

        return;

    }



    while (fgets(line, sizeof(line), file)) {

        struct Date currentDate;

        float cost;



        if (sscanf(line, "%02d/%02d/%04d: %*d units at Rs%f", &currentDate.month, &currentDate.day, &currentDate.year, &cost) == 4) {

            if (currentDate.month == month && currentDate.year == year) {

                weeklyTotal += cost;

                weekDayCount++;



                // Check if we've reached the end of the week (assuming Sunday as the first day of the week)

                if (weekDayCount == 7) {

                    float weeklyAverage = weeklyTotal / 7.0;



                    fprintf(weeklyAverageFile, "Week %d - Avg: Rs%.2f\n", weekNumber, weeklyAverage);



                    weeklyTotal = 0.0;

                    weekDayCount = 0;

                    weekNumber++;

                }

            }

        }

    }



    fclose(file);

    fclose(weeklyAverageFile); // Close the weekly average file

}





void printProductWithMostSales() {

    FILE *file = fopen(WEEKLY_AVERAGE_FILE, "r");

    if (file == NULL) {

        perror("Error opening weekly average file");

        return;

    }



    // Initialize variables to track the product with the most sales

    struct ProductSales mostSoldProduct;

    mostSoldProduct.weeklyAverage = 0;



    char line[MAX_DATA_LENGTH];



    while (fgets(line, sizeof(line), file)) {

        struct ProductSales productSales;



        // Parse the product name and weekly average from the line

        if (sscanf(line, "%[^:]: %f", productSales.productName, &productSales.weeklyAverage) == 2) {

            // Check if this product has a higher weekly average

            if (productSales.weeklyAverage > mostSoldProduct.weeklyAverage) {

                // Update the most sold product

                mostSoldProduct = productSales;

            }

        }

    }



    // Close the file

    fclose(file);



    // Check if a product with non-zero weekly average was found

    if (mostSoldProduct.weeklyAverage > 0) {

        printf("Product with the most sales:\n");

        printf("Product Name: %s\n", mostSoldProduct.productName);

        printf("Weekly Average Sales: %.2f\n", mostSoldProduct.weeklyAverage);

    } else {

        printf("No product sales data found.\n");

    }





    

    printf("Printing product with the most sales...\n");

}



// Function to calculate the customer's bill

float calculateBill() {

   



    int choice, quantity;

    float totalBill = 0.0;



    // Placeholder menu and billing logic

    printf("Menu:\n");

    printf("1. Vadapav - Rs10\n");

    printf("2. Samoso - Rs12\n");

    printf("3. Burger - Rs20\n");

    printf("4. Pizza - Rs25\n");

    printf("5. Manchurian - Rs10\n");

    printf("6. End Order\n");



    do {

        printf("Enter your choice (1-6): ");

        scanf("%d", &choice);



        if (choice >= 1 && choice <= 5) {

            printf("Enter quantity: ");

            scanf("%d", &quantity);



            switch (choice) {

                case 1:

                    totalBill += 10.0 * quantity;

                    break;

                case 2:

                    totalBill += 12.0 * quantity;

                    break;

                case 3:

                    totalBill += 20.0 * quantity;

                    break;

                case 4:

                    totalBill += 25.0 * quantity;

                    break;

                case 5:

                    totalBill += 10.0 * quantity;

                    break;

            }

        } else if (choice != 6) {

            printf("Invalid choice. Please try again.\n");

        }



    } while (choice != 6);



    return totalBill;

}



int main() {

    int month, year;



    printf("********************************************* WATUMULL RESTAURANT ************************************************************ \n");

    printf("hello user\n");

    printf("MENU WITH CODES AND PRICES\n");

    printf("1. Vadapav - Rs10\n");

    printf("2. Samoso - Rs12\n");

    printf("3. Burger - Rs20\n");

    printf("4. Pizza - Rs25\n");

    printf("5. Manchurian - Rs10\n");

    printf("6. Exit\n");

    printf("Enter the month (1-12): ");

    scanf("%d", &month);

    printf("Enter the year: ");

    scanf("%d", &year);



    if (month < 1 || month > 12) {

        printf("Invalid month input.\n");

        return 1;

    }



    int choice;

    float totalSales = 0;



    do {

        printf("\nMenu:\n");

        printf("1. Print Calendar\n");

        printf("2. Store Data for a Date\n");

        printf("3. Print Data for a Date\n");

        printf("4. Calculate and Store Weekly Average\n");

        printf("5. Print Product with Most Sales\n");

        printf("6. Exit\n");

        printf("Enter your choice: ");

        scanf("%d", &choice);



        switch (choice) {

            case 1:

                printCalendar(month, year);

                break;

            case 2:

            {

                int day;

                printf("Enter the day to store data (1-%d): ", MAX_DAYS_IN_MONTH);

                scanf("%d", &day);



                if (day < 1 || day > MAX_DAYS_IN_MONTH) {

                    printf("Invalid day input.\n");

                    continue;

                }



                struct Date selectedDate = {day, month, year};

                char data[MAX_DATA_LENGTH];

                printf("Enter data and name of cashier for %d/%d/%d: ", selectedDate.month, selectedDate.day, selectedDate.year);

                scanf(" %[^\n]", data);



                printf("START FOR BILLING \n");

                FILE *pf = fopen("total_sales111.txt", "w");

                if (pf == NULL) {

                    perror("Error opening total sales file");

                    break;

                }

                

                while (1) {

                    printf("New customer order:\n");

                    float customerBill = calculateBill();

                    totalSales += customerBill;

                    printf("Customer's Bill: Rs%.2f\n", customerBill);

                    char proceed;

                    printf("Do you want to continue with the next customer (y/n)? ");

                    scanf(" %c", &proceed);



                    if (proceed != 'y' && proceed != 'Y') {

                       fprintf(pf, "%d/%d/%d: Total Sales: Rs%.2f\n", selectedDate.month, selectedDate.day, selectedDate.year, totalSales);



                        printf ("Total Sales: Rs%.2f\n", totalSales);

                        fclose(pf);

                        break;

                    }

                }



                storeData(selectedDate, data);

                printf("Data stored successfully.\n");

            }

            break;



            case 3:

                {

                    int day;

                    printf("Enter the day to print data (1-%d): ", MAX_DAYS_IN_MONTH);

                    scanf("%d", &day);



                    if (day < 1 || day > MAX_DAYS_IN_MONTH) {

                        printf("Invalid day input.\n");

                        continue;

                    }



                    struct Date printDate = {day, month, year};

                    printData(printDate);

                }

                break;

            case 4:

                calculateAndStoreWeeklyAverage(month, year);

                printf("Weekly average calculated and stored.\n");

                break;

            case 5:

                printProductWithMostSales();

                break;

            case 6:

                printf("Exiting program.\n");

                break;

            default:

                printf("Invalid choice. Please try again.\n");

        }



    } while (choice != 6);



    return 0;

}



