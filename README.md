# Booking_Ticket-and-Booking_Seat
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

#define MAX_SEATS 50
#define VIP_SEATS 20
#define REGULAR_SEATS (MAX_SEATS - VIP_SEATS)
#define MOVIE 6
#define SLOT 3
#define MAX_VENUES 3
#define Hash_Size 100

typedef struct Slot {
    char time[35];
    int availableVipSeats;
    int availableRegularSeats;
    struct Slot *next;
} Slot;

typedef struct Movie {
    char title[50];
    char genre[50];
    char venues[MAX_VENUES][50];
    int vipPrice;
    int regularPrice;
    Slot *slots;
} Movie;

typedef struct HashNode {
    char key[50];
    int value;
    struct HashNode *next;
} HashNode;

typedef struct HashMap {
    struct HashNode *table[Hash_Size];
} HashMap;

typedef struct BookingRequest {
    int movie_Index;
    int venue_Index;
    int slot_number;
    int numSeats;
    int numVipSeats;
    int numRegularSeats;
    int priority;
    struct BookingRequest *next;
} BookingRequest;

typedef struct PriorityQueue {
    BookingRequest *front;
} PriorityQueue;

void initializeMovies(Movie movies[MOVIE]);
int hashfunction(const char *key);
void initializeHashMap(HashMap *map);
void insertHash(HashMap *map, const char *key, int value);
int deleteHash(HashMap *map, const char *key);
void Display_Movies(Movie movies[]);
void Display_Venues(Movie movie);
void display_Slot(Movie *movie, const char *selectedVenue);
void initializeQueue(PriorityQueue *queue);
void inqueue(PriorityQueue *queue, int movie_Index, int venue_Index, int slot_number, int numSeats, int priority);
void displaySeats(HashMap *seatMap, const char *seatType, int totalSeats);
void bookSeats(Movie movies[], HashMap *seatMap, BookingRequest *request, int *selectedSeats);
void Book_Ticket(Movie movies[], PriorityQueue *queue, HashMap *seatMap);


int hashfunction(const char *key) {
    int hash = 0;
    while (*key) {
        hash = (hash * 31 + *key) % Hash_Size;
        key++;
    }
    return hash;
}

void initializeHashMap(HashMap *map) {
    for (int i = 0; i < Hash_Size; i++) {
        map->table[i] = NULL;
    }
}

void insertHash(HashMap *map, const char *key, int value) {
    int index = hashfunction(key);
    HashNode *newNode = (HashNode *)malloc(sizeof(HashNode));
    if (!newNode) {
        printf("Memory allocation failed.\n");
        exit(1); // Exit on memory allocation failure
    }
    strcpy(newNode->key, key);
    newNode->value = value;
    newNode->next = map->table[index];
    map->table[index] = newNode;
}

int searchHash(HashMap *map, const char *key) {
    int index = hashfunction(key);
    HashNode *current = map->table[index];
    while (current) {
        if (strcmp(current->key, key) == 0) {
            return current->value;
        }
        current = current->next;
    }
    return -1;
}

int deleteHash(HashMap *map, const char *key) {
    int index = hashfunction(key); // Calculate hash index
    HashNode *current = map->table[index];
    HashNode *prev = NULL;

    while (current != NULL) {
        if (strcmp(current->key, key) == 0) {
            if (prev == NULL) {
                map->table[index] = current->next; // Remove head node
            } else {
                prev->next = current->next; // Remove middle or last node
            }
            free(current); // Free the memory
            return 1;
        }
        prev = current;
        current = current->next;
    }

    // If key not found
    printf("Key %s not found in the hash map.\n", key);
}


void initializeMovies(Movie movies[MOVIE]) {
    strcpy(movies[0].title, "Smile 2");
    strcpy(movies[0].genre, "Horror");
    strcpy(movies[0].venues[0], "PVR FORUM MALL");
    strcpy(movies[0].venues[1], "Fun Cinemas");
    strcpy(movies[0].venues[2], "Manasa Theatre");
    movies[0].vipPrice = 500;
    movies[0].regularPrice = 200;
    
    Slot *slot1_movie0 = (Slot *)malloc(sizeof(Slot));
    strcpy(slot1_movie0->time, "9:00AM-12:00PM");
    slot1_movie0->availableVipSeats=VIP_SEATS;
    slot1_movie0->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    Slot *slot2_movie0 = (Slot *)malloc(sizeof(Slot));
    strcpy(slot2_movie0->time, "2:00PM-5:00PM");
    slot2_movie0->availableVipSeats=VIP_SEATS;
    slot2_movie0->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    Slot *slot3_movie0 = (Slot *)malloc(sizeof(Slot));
    strcpy(slot3_movie0->time, "6:00PM-9:00PM");
    slot3_movie0->availableVipSeats= VIP_SEATS;
    slot3_movie0->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    slot1_movie0->next=slot2_movie0;
    slot2_movie0->next=slot3_movie0;
    slot3_movie0->next=NULL;
    
    movies[0].slots = slot1_movie0;
    
    strcpy(movies[1].title, "Laapataa Ladies");
    strcpy(movies[1].genre, "Drama");
    strcpy(movies[1].venues[0], "AMB Cinemas");
    strcpy(movies[1].venues[1], "Victory Cinemas");
    strcpy(movies[1].venues[2], "Akash Cinemas");
    movies[1].vipPrice = 200;
    movies[1].regularPrice = 100;
    
    Slot *slot1_movie1 = (Slot *)malloc(sizeof(Slot));
    strcpy(slot1_movie1->time, "9:00AM-12:00PM");
    slot1_movie1->availableVipSeats= VIP_SEATS;
    slot1_movie1->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    Slot *slot2_movie1= (Slot *)malloc(sizeof(Slot));
    strcpy(slot2_movie1->time, "2:00PM-5:00PM");
    slot2_movie1->availableVipSeats= VIP_SEATS;
    slot2_movie1->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    Slot *slot3_movie1 = (Slot *)malloc(sizeof(Slot));
    strcpy(slot3_movie1->time, "6:00PM-9:00PM");
    slot3_movie1->availableVipSeats= VIP_SEATS;
    slot3_movie1->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    slot1_movie1->next=slot2_movie1;
    slot2_movie1->next=slot3_movie1;
    slot3_movie1->next=NULL;
 
    movies[1].slots = slot1_movie1;
 
    strcpy(movies[2].title, "Lucky Baskha₹");
    strcpy(movies[2].genre, "Triller");
    strcpy(movies[2].venues[0], "Cinepolies");
    strcpy(movies[2].venues[1], "INOX");
    strcpy(movies[2].venues[2], "PVR Cinemas Gold");
    movies[2].vipPrice = 600;
    movies[2].regularPrice = 300;
    
    Slot *slot1_movie2 = (Slot *)malloc(sizeof(Slot));
    strcpy(slot1_movie2->time, "9:00AM-12:00PM");
    slot1_movie2->availableVipSeats= VIP_SEATS;
    slot1_movie2->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    Slot *slot2_movie2= (Slot *)malloc(sizeof(Slot));
    strcpy(slot2_movie2->time, "2:00PM-5:00PM");
    slot2_movie2->availableVipSeats= VIP_SEATS;
    slot2_movie2->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    Slot *slot3_movie2 = (Slot *)malloc(sizeof(Slot));
    strcpy(slot3_movie2->time, "6:00PM-9:00PM");
    slot3_movie2->availableVipSeats= VIP_SEATS;
    slot3_movie2->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    slot1_movie2->next=slot2_movie2;
    slot2_movie2->next=slot3_movie2;
    slot3_movie2->next=NULL;
    
   movies[2].slots = slot1_movie2;
    
    strcpy(movies[3].title, "Teri Baaton Mein Aisa Uljha Jiya");
    strcpy(movies[3].genre, "Comedy");
    strcpy(movies[3].venues[0], "Urvashi Theatre");
    strcpy(movies[3].venues[1], "Everest Theatre");
    strcpy(movies[3].venues[2], "Cauvery Theatre");
    movies[3].vipPrice = 250;
    movies[3].regularPrice = 150;
    
    Slot *slot1_movie3 = (Slot *)malloc(sizeof(Slot));
    strcpy(slot1_movie3->time, "9:00AM-12:00PM");
    slot1_movie3->availableVipSeats= VIP_SEATS;
    slot1_movie3->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    Slot *slot2_movie3= (Slot *)malloc(sizeof(Slot));
    strcpy(slot2_movie3->time, "2:00PM-5:00PM");
    slot2_movie3->availableVipSeats= VIP_SEATS;
    slot2_movie3->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    Slot *slot3_movie3= (Slot *)malloc(sizeof(Slot));
    strcpy(slot3_movie3->time, "6:00PM-9:00PM");
    slot3_movie3->availableVipSeats= VIP_SEATS;
    slot3_movie3->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    slot1_movie3->next=slot2_movie3;
    slot2_movie3->next=slot3_movie3;
    slot3_movie3->next=NULL;
    
   movies[3].slots = slot1_movie3;
    
    strcpy(movies[4].title, "Amaran");
    strcpy(movies[4].genre, "Action");
    strcpy(movies[4].venues[0], "IMAX");
    strcpy(movies[4].venues[1], "S.B.J");
    strcpy(movies[4].venues[2], "The Binge Town");
    movies[4].vipPrice = 150;
    movies[4].regularPrice = 100;
    
    Slot *slot1_movie4 = (Slot *)malloc(sizeof(Slot));
    strcpy(slot1_movie4->time, "9:00AM-12:00PM");
    slot1_movie4->availableVipSeats= VIP_SEATS;
    slot1_movie4->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    Slot *slot2_movie4= (Slot *)malloc(sizeof(Slot));
    strcpy(slot2_movie4->time, "2:00PM-5:00PM");
    slot2_movie4->availableVipSeats= VIP_SEATS;
    slot2_movie4->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    Slot *slot3_movie4= (Slot *)malloc(sizeof(Slot));
    strcpy(slot3_movie4->time, "6:00PM-9:00PM");
    slot3_movie4->availableVipSeats= VIP_SEATS;
    slot3_movie4->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    slot1_movie4->next=slot2_movie4;
    slot2_movie4->next=slot3_movie4;
    slot3_movie4->next=NULL;
    
   movies[4].slots = slot1_movie4;
    
    strcpy(movies[5].title, "Mungaru Male");
    strcpy(movies[5].genre, "Romatic");
    strcpy(movies[5].venues[0], "Pruthvi");
    strcpy(movies[5].venues[1], "Guru");
    strcpy(movies[5].venues[2], "Balaji Cinemas");
    movies[5].vipPrice = 350;
    movies[5].regularPrice = 200;
    
    Slot *slot1_movie5 = (Slot *)malloc(sizeof(Slot));
    strcpy(slot1_movie5->time, "9:00AM-12:00PM");
    slot1_movie5->availableVipSeats= VIP_SEATS;
    slot1_movie5->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    Slot *slot2_movie5= (Slot *)malloc(sizeof(Slot));
    strcpy(slot2_movie5->time, "2:00PM-5:00PM");
    slot2_movie5->availableVipSeats= VIP_SEATS;
    slot2_movie5->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    Slot *slot3_movie5 = (Slot *)malloc(sizeof(Slot));
    strcpy(slot3_movie5->time, "6:00PM-9:00PM");
    slot3_movie5->availableVipSeats= VIP_SEATS;
    slot3_movie5->availableRegularSeats= MAX_SEATS - VIP_SEATS;
    
    slot1_movie5->next=slot2_movie5;
    slot2_movie5->next=slot3_movie5;
    slot3_movie5->next=NULL;
    
   movies[5].slots = slot1_movie5;
}

void Display_Movies(Movie movies[]) {
    printf("*********************************************\n");
    printf("             !!Explore Movies🎬!!             \n");
    printf("*********************************************\n");
    for (int i = 0; i < MOVIE; i++) {
        printf("%d. %s (%s)\n", i + 1, movies[i].title, movies[i].genre);
    }
    printf("*********************************************\n");
}

void Display_Venues(Movie movie) {
    for (int i = 0; i < MAX_VENUES; i++) {
        printf("%d. %s\n", i + 1, movie.venues[i]);
    }
}

void display_Slot(Movie *movie, const char *selectedVenue)
{
    printf("------------------------------------------------------------\n");
    printf("Available slots for '%s' in '%s'\n", movie->title, selectedVenue);
    printf("------------------------------------------------------------\n");
    printf("+-----+------------------+-------------------+-------------------+\n"); 
    printf("| Slot | Time             | VIP Seats         | Regular Seats    |\n");
    printf("+-----+------------------+-------------------+-------------------+\n");
        
    Slot *currentSlot = movie->slots;
    int slotNumber = 1;
    int totalSlots = 0; // To track the number of available slots
    
    // Display all available slots
    while (currentSlot != NULL)
    {
        printf("| %-3d | %-16s | %-17d | %-17d |\n", 
               slotNumber++, 
               currentSlot->time, 
               currentSlot->availableVipSeats, 
               currentSlot->availableRegularSeats);
        currentSlot = currentSlot->next;
        totalSlots++;
    }
    printf("+-----+------------------+-------------------+-------------------+\n");

    
}
void initializeQueue(PriorityQueue *queue) {
    queue->front = NULL;
}

void inqueue(PriorityQueue *queue, int movie_Index, int venue_Index, int slot_number, int numSeats, int priority) {
    BookingRequest *newRequest = (BookingRequest *)malloc(sizeof(BookingRequest));
    if (!newRequest) {
        printf("Memory allocation failed.\n");
        exit(1);
    }
    newRequest->movie_Index = movie_Index;
    newRequest->venue_Index = venue_Index;
    newRequest->slot_number = slot_number;
    newRequest->numSeats = numSeats;
    newRequest->numVipSeats = 0; // Initialize
    newRequest->numRegularSeats = 0; // Initialize
    newRequest->priority = priority;
    newRequest->next = NULL;

    if (queue->front == NULL || queue->front->priority > priority) {
        newRequest->next = queue->front;
        queue->front = newRequest;
    } else {
        BookingRequest *current = queue->front;
        while (current->next != NULL && current->next->priority <= priority) {
            current = current->next;
        }
        newRequest->next = current->next;
        current->next = newRequest;
    }
}

BookingRequest *outqueue(PriorityQueue *queue) {
    if (queue->front == NULL) {
        return NULL;
    }
    BookingRequest *request = queue->front;
    queue->front = queue->front->next;
    return request;
}

void displaySeats(HashMap *seatMap, const char *seatType, int totalSeats)
{
    int rows = totalSeats / 5; // Define rows, assuming 5 seats per row for simplicity
    if (totalSeats % 5 != 0)
        rows++; // Add an extra row for remaining seats

    printf("------------------------------------------------------------\n");
    printf("Available %s Seats (⭕️= Available, 🪙 = Booked):\n", seatType);
    printf("------------------------------------------------------------\n");

    int seatNumber = 1;
    for (int i = 0; i < rows; i++)
    {
        for (int j = 0; j < 5; j++) // 5 seats per row
        {
            if (seatNumber > totalSeats)
                break;

            char seatKey[20];
            sprintf(seatKey, "%s%d", seatType, seatNumber);

            if (searchHash(seatMap, seatKey) == -1) // Seat is available
            {
                printf("%s%-3d ","⭕️", seatNumber);
            }
            else // Seat is booked
            {
                printf("%s%-3d ", "🪙", seatNumber);
            }
            seatNumber++;
        }
        printf("\n");
    }
    printf("------------------------------------------------------------\n");
}

void bookSeats(Movie movies[], HashMap *seatMap, BookingRequest *request, int *selectedSeats) {
    Slot *currentSlot = movies[request->movie_Index].slots;
    for (int i = 0; i < request->slot_number; i++) {
        currentSlot = currentSlot->next;
    }

    // Determine seat type based on priority
    char *seatType = request->priority == 1 ? "VIP" : "Regular";
    int availableSeats = request->priority == 1 ? currentSlot->availableVipSeats : currentSlot->availableRegularSeats;

    // Check if enough seats are available
    if (availableSeats < request->numSeats) {
        printf("\nNot enough %s seats available!! Booking failed.\n", seatType);
        return;
    }

    printf("Enter the seat numbers (🪑) you want to book: \n");
    printf("Enter %d seat numbers separated by spaces (e.g., 1 2 3): ", request->numSeats);

    int validSeatCount = 0;
    int invalidSeats[request->numSeats]; // To track invalid seats
    int invalidCount = 0; // To count invalid seats

    // Input all seats at once
    while (validSeatCount < request->numSeats) {
        for (int i = 0; i < request->numSeats; i++) {
            if (scanf("%d", &selectedSeats[i]) != 1) {
                printf("Invalid input. Please enter valid seat numbers.\n");
                return;
            }
        }

        // Check all seats for validity
        for (int i = 0; i < request->numSeats; i++) {
            char seatKey[10];
            sprintf(seatKey, "%s%d", seatType, selectedSeats[i]);

            if (searchHash(seatMap, seatKey) != -1) { // Seat is already booked
                invalidSeats[invalidCount] = selectedSeats[i]; // Add to invalid seats list
                invalidCount++;
            }
        }

        // If there are invalid seats, show them and ask for new seat numbers
        if (invalidCount > 0) {
            printf("The following seats are already booked:\n");
            for (int i = 0; i < invalidCount; i++) {
                printf("%s%d ", seatType, invalidSeats[i]);
            }
            printf("\nPlease enter different seat numbers.\n");
            invalidCount = 0; // Reset invalid count for the next attempt
        } else {
            // All seats are valid, book them
            for (int i = 0; i < request->numSeats; i++) {
                char seatKey[10];
                sprintf(seatKey, "%s%d", seatType, selectedSeats[i]);
                insertHash(seatMap, seatKey, 1); // Mark seat as booked

                if (request->priority == 1)
                    currentSlot->availableVipSeats--;
                else
                    currentSlot->availableRegularSeats--;

                printf("%s%d successfully booked 🎉🤩.\n", seatType, selectedSeats[i]);
            }
            validSeatCount = request->numSeats; // Mark seats as fully booked
        }
    }
}

void Book_Ticket(Movie movies[], PriorityQueue *queue, HashMap *seatMap)
{
    int movie_Index, venue_Index, slot_number, numSeats, priority;

    printf("======================= !!BOOK A TICKET🎫!!=======================\n");

    // Display movies and get user input
    printf("************************************************************\n");
    printf("🍿 Is Your Popcorn Ready? Let's Find the Perfect Movie! 🎬🎥\n");
    printf("************************************************************\n");
    for (int i = 0; i < MOVIE; i++)
    {
        printf("%d. %s (%s)\n", i + 1, movies[i].title, movies[i].genre);
    }
    printf("*************************************************************\n");
    printf("Choose a movie you would love😍 to watch: ");
    scanf("%d", &movie_Index);
    movie_Index--;

    // Display venues
    printf("---------------------------------------------------------\n");
    printf("Available Venues for '%s': \n", movies[movie_Index].title);
    printf("----------------------------------------------------------\n");
    Display_Venues(movies[movie_Index]);
    printf("Choose the venue you love to watch '%s': ", movies[movie_Index].title);
    scanf("%d", &venue_Index);
    char *selectedVenue = movies[movie_Index].venues[venue_Index - 1];
    printf("You have selected: %s\n", selectedVenue);

    // Select slot
    int validSlot = 0;
    while (!validSlot)
    {
        display_Slot(&movies[movie_Index], selectedVenue);
        printf("Select the slot you would love to watch '%s': ", movies[movie_Index].title);
        scanf("%d", &slot_number);

        if (slot_number >= 1 && slot_number <= 3) // Assuming a maximum of 3 slots
        {
            validSlot = 1;
        }
        else
        {
            printf("Invalid slot number. Please choose a valid slot (1-3).\n");
        }
    }


    // Ask for seat quantity and type
    printf("------------------------------------------------------------\n");
    printf("How many seats would you like to book?\n");
    scanf("%d", &numSeats);
    printf("\nVIP(👑) Ticket Price:₹%d\n", movies[movie_Index].vipPrice);
    printf("Regular(🪑) Ticket Price:₹%d\n", movies[movie_Index].regularPrice);
    printf("\nPick your perfect spot to enjoy the show! 🎫✨\n");
    printf("1. VIP (👑)\n");
    printf("2. Regular (🪑)\n");
    printf("What's your choice🤔?\n");
    scanf("%d", &priority);

    // Add booking request to the queue
    inqueue(queue, movie_Index, venue_Index, slot_number, numSeats, priority);

    // Process the booking request
    BookingRequest *request = outqueue(queue);
    if (request)
    {
        int selectedSeats[numSeats]; // Ensure correct size
        if (request->priority == 1)
        {
            displaySeats(seatMap, "VIP", VIP_SEATS);
        }
        else
        {
            displaySeats(seatMap, "Regular", REGULAR_SEATS);
        }

        bookSeats(movies, seatMap, request, selectedSeats);

        printf("----------------------------------------------\n");
        printf("          Booking Summary                     \n");
        printf("----------------------------------------------\n");
        printf(" Movie: %s                     \n", movies[movie_Index].title);
        printf(" Venue: %s                     \n", selectedVenue);
        printf(" Slot No:  %d                           \n",slot_number);
        printf(" Number of Seats: %d                     \n", numSeats);
        printf(" Total Price: ₹%d                    \n", numSeats * (priority == 1 ? movies[movie_Index].vipPrice : movies[movie_Index].regularPrice));
        printf("-------------------------------------------------------\n");

        char confirmation[10];
        printf("Do you want to confirm the booking? ");
        scanf("%s", confirmation);

        if (strcmp(confirmation, "yes") == 0 || strcmp(confirmation, "YES") == 0 || strcmp(confirmation, "y") == 0)
        {
            printf("Your ticket is successfully booked! 🎉 You are all set to enjoy the movie!🍿🎬\n");
        }
        else
        {
            printf("\nBooking has been cancelled.\n");
            printf("Skipping the ticket? 😅 Looks like the movie will have to carry on without its VIP. We'll miss you, but we know you'll be back for the encore! 🎥💫\n");


            for (int i = 0; i < request->numSeats; i++)
            {
                char seatKey[10];
                sprintf(seatKey, "%s%d", request->priority == 1 ? "VIP" : "Regular", selectedSeats[i]);
                deleteHash(seatMap, seatKey); // Unbook the seat from HashMap
            }
            

            // Restore seat count
            Slot *currentSlot = &movies[request->movie_Index].slots[request->slot_number];
            if (request->priority == 1)
            currentSlot->availableVipSeats += request->numSeats;
            else
                currentSlot->availableRegularSeats += request->numSeats;
        }

        free(request);
    }
    else
    {
        printf("No booking request to process.\n");
    }
    printf("\n");
}




int main() 
{
    Movie movies[MOVIE];
    PriorityQueue queue;
    HashMap seatMap;
    initializeMovies(movies);
    initializeQueue(&queue);
    initializeHashMap(&seatMap);

    int choice;
    for (;;) {
        printf("==================================================\n");
        printf("Welcome to our Movie ticket booking Program 🎬🍿🎫\n");
        printf("==================================================\n");
        printf("1. Explore Movies\n");
        printf("2. Book the tickets\n");
        printf("3. Exit\n");
        printf("---------------------------------------------------\n");
        printf("What would you like to do next?🧐\n");

        if (scanf("%d", &choice) != 1) {
            printf("Invalid input. Please enter a number.\n");
            while (getchar() != '\n');
            continue; // Go back to the menu
        }

        switch (choice) {
            case 1:
                Display_Movies(movies);
                break;
            case 2:
                Book_Ticket(movies, &queue, &seatMap);
                break;
            case 3:
                printf("\n===========================================\n");
                printf("  !!Thank you for using the Booking System!!\n");
                printf("      Have a great day ahead!!🎬\n");
                printf("===========================================\n");
                return 0; // Correctly exit the program
            default:
                printf("Invalid choice. Please try again.\n");
        }
    }
    return 0; // Added return for completeness
}
