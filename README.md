#include<stdio.h>
#include<string.h>
#include<time.h>

char en[50], st[50], et[50], da[50], ve[50], participantName[50], participantEmail[50];

// Hardcoded username and password
const char username[] = "admin";
const char password[] = "password123";

void login() {
    char enteredUsername[50], enteredPassword[50];

    printf("Enter username: ");
    scanf("%s", enteredUsername);
    printf("Enter password: ");
    scanf("%s", enteredPassword);

    // Check if the entered username and password match
    if (strcmp(enteredUsername, username) == 0 && strcmp(enteredPassword, password) == 0) {
        printf("Login successful!\n");
    } else {
        printf("Invalid username or password. Try again.\n");
        exit(0); // Exit the program if login fails
    }
}

void addEvent() {
    FILE *fp;
    fp = fopen("event.txt", "a");
    if (fp == NULL) {
        printf("Can't open the file");
        return;
    }
    printf("Enter Event name, Date (YYYY-MM-DD), Start Time, End Time, Venue: ");
    scanf("%s %s %s %s %s", en, da, st, et, ve);
    fprintf(fp, "%s\t%s\t%s\t%s\t%s\n", en, da, st, et, ve);
    fclose(fp);
}

void updateEvent() 
{
    FILE *fp, *ft;
    fp = fopen("event.txt", "r");
    ft = fopen("temp.txt", "w");
    if (fp == NULL || ft == NULL)  
     {
        printf("Can't open the file");
        return;
    }
    char n[50];
    fflush(stdin);
    printf("Enter Event Name you want to Modify: ");
    scanf("%s", n);
    while (fscanf(fp, "%s %s %s %s %s", en, da, st, et, ve) != EOF) 
    {
        if (strcmp(en, n) == 0) {
            printf("Enter New Event name, Date (YYYY-MM-DD), Start Time, End Time, Venue: ");
            scanf("%s %s %s %s %s", en, da, st, et, ve);
        }
        fprintf(ft, "%s\t%s\t%s\t%s\t%s\n", en, da, st, et, ve);
    }
    fclose(fp);
    fclose(ft);
    remove("event.txt");
    rename("temp.txt", "event.txt");
}

void deleteEvent() 
{
    FILE *fp, *ft;
    fp = fopen("event.txt", "r");
    ft = fopen("temp.txt", "w");
    if (fp == NULL || ft == NULL) {
        printf("Can't open the file");
        return;
    }
    char n[50];
    fflush(stdin);
    printf("Enter Event Name you want to Delete: ");
    scanf("%s", n);
    while (fscanf(fp, "%s %s %s %s %s", en, da, st, et, ve) != EOF) {
        if (strcmp(en, n) != 0) {
            fprintf(ft, "%s\t%s\t%s\t%s\t%s\n", en, da, st, et, ve);
        }
    }
    fclose(fp);
    fclose(ft);
    remove("event.txt");
    rename("temp.txt", "event.txt");
}

void viewReport() {
    FILE *fp;
    fp = fopen("event.txt", "r");
    getch();
    if (fp == NULL) {
        printf("Can't open the file");
        getch();
        return;
    }

    printf("Event Name\tDate\t\tStart Time\tEnd Time\tVenue\n");
    printf("\n");
    while (fscanf(fp, "%s %s %s %s %s", en, da, st, et, ve) != EOF) {
        printf("%s\t\t%s\t%s\t\t%s\t\t%s\n", en, da, st, et, ve);
    }
    getch();
    fclose(fp);
}

void sendNotifications() 
{
    FILE *fp;
    fp = fopen("event.txt", "r");
    if (fp == NULL) {
        printf("Can't open the file");
        return;
    }

    time_t now = time(NULL);
    struct tm *local = localtime(&now);
    char today[11], tomorrow[11];

    // Format today's and tomorrow's dates as YYYY-MM-DD
    strftime(today, sizeof(today), "%Y-%m-%d", local);
    local->tm_mday += 1;
    mktime(local); // Normalize date
    strftime(tomorrow, sizeof(tomorrow), "%Y-%m-%d", local);

    printf("\nNotifications for Upcoming Events:\n");
    printf("\n");
    int found = 0;
    while (fscanf(fp, "%s %s %s %s %s", en, da, st, et, ve) != EOF) {
        if (strcmp(da, today) == 0) {
            printf("[Today] %s at %s in %s\n", en, st, ve);
            found = 1;
        } else if (strcmp(da, tomorrow) == 0) {
            printf("[Tomorrow] %s at %s in %s\n", en, st, ve);
            found = 1;
        }
    }
    if (!found) {
        printf("No upcoming events for today or tomorrow.\n");
    }
    fclose(fp);
}

void registerParticipant() {
    FILE *fp;
    fp = fopen("registrations.txt", "a");
    if (fp == NULL) {
        printf("Can't open the file for registrations");
        return;
    }

    printf("Enter Event Name: ");
    scanf("%s", en);
    printf("Enter Participant Name: ");
    scanf("%s", participantName);
    printf("Enter Participant Email: ");
    scanf("%s", participantEmail);

    fprintf(fp, "%s\t%s\t%s\n", en, participantName, participantEmail);
    printf("Participant registered successfully.\n");
    fclose(fp);
}

void viewRegistrations() {
    FILE *fp;
    fp = fopen("registrations.txt", "r");
    if (fp == NULL) {
        printf("No registrations found.");
        return;
    }

    printf("Event Name\tParticipant Name\tParticipant Email\n");
    printf("\n");
    while (fscanf(fp, "%s %s %s", en, participantName, participantEmail) != EOF) {
        printf("%s\t\t%s\t\t%s\n", en, participantName, participantEmail);
  
    }
    fclose(fp);
}

void searchAndFilterEvents() {
    FILE *fp;
    fp = fopen("event.txt", "r");
    if (fp == NULL) {
        printf("Can't open the file");
        return;
    }

    char filterDate[11], filterVenue[50];
    printf("Enter Date to filter (YYYY-MM-DD) or 'all' for no filter: ");
    scanf("%s", filterDate);
    printf("Enter Venue to filter or 'all' for no filter: ");
    scanf("%s", filterVenue);

    printf("\nFiltered Events:\n");
    printf("Event Name\tDate\t\tStart Time\tEnd Time\tVenue\n");
    printf("\n");
    int found = 0;
    while (fscanf(fp, "%s %s %s %s %s", en, da, st, et, ve) != EOF) {
        if ((strcmp(filterDate, "all") == 0 || strcmp(da, filterDate) == 0) &&
            (strcmp(filterVenue, "all") == 0 || strcmp(ve, filterVenue) == 0)) {
            printf("%s\t\t%s\t%s\t\t%s\t\t%s\n", en, da, st, et, ve);
            found = 1;
        }
    }
    if (!found) {
        printf("No events found matching the criteria.\n");
    }
    fclose(fp);
}

int main() {
    login();  // Call login function before displaying the menu

    int choice;

    while (1) {
        system("cls");
        printf("\t\tE V E N T      M A N A G E M E N T     P R O J E C T\n");
        printf("\n");
        printf("1.\tAdd Event\n");
        printf("2.\tUpdate Event\n");
        printf("3.\tDelete Event\n");
        printf("4.\tView Event\n");
        printf("5.\tSend Notifications\n");
        printf("6.\tRegister Participant\n");
        printf("7.\tView Registrations\n");
        printf("8.\tSearch and Filter Events\n");
        printf("9.\tExit\n");
        printf("\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);
        printf("\n");

        switch (choice) {
            case 1:
                addEvent();
                break;
            case 2:
                updateEvent();
                break;
            case 3:
                deleteEvent();
                break;
            case 4:
                viewReport();
                getch();
                break;
            case 5:
                sendNotifications();
                getch();
                break;
            case 6:
                registerParticipant();
                getch();
                break;
            case 7:
                viewRegistrations();
                getch();
                break;
            case 8:
                searchAndFilterEvents();
                getch();
                break;
            case 9:
                printf("Exiting the program.\n");
                return 0;
            default:
                printf("Invalid choice. Please try again.\n");
        }
    }
}# FirstYearProject1
