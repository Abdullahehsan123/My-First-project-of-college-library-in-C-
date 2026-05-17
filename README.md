# My-First-project-of-college-library-in-C-
College library 

#include<stdio.h>
#include<stdlib.h>
#include<time.h>
#include<string.h>
#include<conio.h>

struct books{
    int id;
    char bookName[50];
    char authorName[50];
    char date[12];
}b;

struct student{
    int id;
    char sName[50];
    char sClass[50];
    int sRoll;
    char bookName[50];
    char date[12];
    char returnDate[12];
}s;

FILE *fp;



//function to add book
void addbook(){
    char myDate[12];
    time_t t = time(NULL);
    struct tm tm = *localtime(&t);
    sprintf(myDate, "%02d/%02d/%d", tm.tm_mday, tm.tm_mon+1, tm.tm_year + 1900);
    strcpy(b.date, myDate);

    fp = fopen("books.txt", "ab");

    printf("Enter book id: ");
    scanf("%d", &b.id);

    printf("Enter book name: ");
    fflush(stdin);
    gets(b.bookName);

    printf("Enter author name: ");
    fflush(stdin);
    gets(b.authorName);

    printf("Book Added Successfully");

    fwrite(&b, sizeof(b), 1, fp);
    fclose(fp);
}
//function to display all book
void booklist(){
    system("cls");
    printf("~~~ Available Books ~~~\n");
    printf("%-10s %-30s %-20s %s\n\n", "Book id", "Book Name", "Author", "Date");

    fp = fopen("books.txt", "rb");
    if(fp == NULL){
        printf("No books found!\n");
        return;
    }
    
    while(fread(&b, sizeof(b), 1, fp) == 1){
        printf("%-10d %-30s %-20s %s\n", b.id, b.bookName, b.authorName, b.date);
    }

    fclose(fp);
}
//function to del book
void del(){
    int id, f=0;
    system("cls");
    printf("~~~Remove Books~~~\n");
    printf("Enter Book id to remove: ");
    scanf("%d", &id);

    FILE *ft;

    fp = fopen("books.txt", "rb");
    ft = fopen("temp.txt", "wb");

    while(fread(&b, sizeof(b), 1, fp) == 1){
        if(id == b.id){
            f=1;
        }else{
            fwrite(&b, sizeof(b), 1, ft);
        }
    }

    if(f==1){
        printf("\n\nDeleted Successfully.");
    }else{
        printf("\n\nRecord Not Found !");
    }

    fclose(fp);
    fclose(ft);

    remove("books.txt");
    rename("temp.txt", "books.txt");
}

// Search Book Function 
void searchbook(){
    char name[50];
    int found=0;
    
    system("cls");
    printf("~~~Search Books~~~\n");
    printf("Enter Book Name: ");
    fflush(stdin);
    gets(name);
    
    fp = fopen("books.txt", "rb");
    if(fp == NULL){
        printf("No books found in library!\n");
        return;
    }
    
    printf("\n========================================\n");
    printf("           SEARCH RESULTS\n");
    printf("========================================\n");
    
    while(fread(&b, sizeof(b), 1, fp) == 1){
        if(strcmp(b.bookName, name) == 0){  
        
            printf("Book ID: %d\n", b.id);
            printf("Book Name: %s\n", b.bookName);
            printf("Author Name: %s\n", b.authorName);
            printf("Added Date: %s\n", b.date);
            printf("----------------------------------------\n");
            found = 1;
        }
    }
    
    if(found == 0){
        printf("No book found with name: %s\n", name);
    }
    
    fclose(fp);
}
//function to issue book by name only
void issuebook(){
    char myDate[12];
    time_t t = time(NULL);
    struct tm tm = *localtime(&t);
    sprintf(myDate, "%02d/%02d/%d", tm.tm_mday, tm.tm_mon+1, tm.tm_year + 1900);
    strcpy(s.date, myDate);

    int f=0;
    char searchName[100];

    system("cls");
    printf("<== Issue Books ==>\n\n");

    printf("Enter Book Name to issue: ");
    fflush(stdin);
    gets(searchName);

    //Check if book exists with given name
    fp = fopen("books.txt", "rb");
    
    if(fp == NULL){
        printf("Error: books.txt file not found!\n");
        printf("Please add some books first.\n\n");
        getch();  
        return;
    }

    while(fread(&b, sizeof(b), 1, fp) == 1){
        
        if(strcmp(b.bookName, searchName) == 0){
            strcpy(s.bookName, b.bookName);
            f=1;
            break;
        }
    }
    fclose(fp);

    if(f==0){
        printf("No book found with this name\n");
        printf("Please try again...\n\n");
        getch();  
        return;
    }

    fp = fopen("issue.txt", "ab");
    
    if(fp == NULL){
        printf("Error: Cannot create issue.txt file!\n");
        getch();  
        return;
    }

    printf("Enter Student Name: ");
    fflush(stdin);
    gets(s.sName);

    printf("Enter Student Class: ");
    fflush(stdin);
    gets(s.sClass);

    printf("Enter Student Roll: ");
    scanf("%d", &s.sRoll);
    
    
    strcpy(s.returnDate, "Not Returned");

    printf("\nBook Issued Successfully!\n");
    printf("Book Name: %s\n", s.bookName);
    printf("Issue Date: %s\n", s.date);

    fwrite(&s, sizeof(s), 1, fp);
    fclose(fp);
    
    printf("\nPress any key to continue...");
    getch();  
}
//function to return book
void returnbook(){
    char myDate[12];
    time_t t = time(NULL);
    struct tm tm = *localtime(&t);
    sprintf(myDate, "%02d/%02d/%d", tm.tm_mday, tm.tm_mon+1, tm.tm_year + 1900);
    
    int id, roll, found=0;
    FILE *fp1, *fp2;
    
    system("cls");
    printf("~~~Return Books~~~\n");
    
    printf("Enter Book id to return: ");
    scanf("%d", &id);
    
    printf("Enter Student Roll Number: ");
    scanf("%d", &roll);
    
    fp1 = fopen("issue.txt", "rb");
    fp2 = fopen("temp.txt", "wb");
    
    if(fp1 == NULL){
        printf("No issued books found!\n");
        return;
    }
    
    while(fread(&s, sizeof(s), 1, fp1) == 1){
        if(s.id == id && s.sRoll == roll && strcmp(s.returnDate, "Not Returned") == 0){
            printf("\n========================================\n");
            printf("        BOOK RETURN DETAILS\n");
            printf("========================================\n");
            printf("Book ID: %d\n", s.id);
            printf("Book Name: %s\n", s.bookName);
            printf("Student Name: %s\n", s.sName);
            printf("Student Class: %s\n", s.sClass);
            printf("Student Roll: %d\n", s.sRoll);
            printf("Issue Date: %s\n", s.date);
            printf("Return Date: %s\n", myDate);
            printf("========================================\n");
            
            strcpy(s.returnDate, myDate);
            found = 1;
        }
        fwrite(&s, sizeof(s), 1, fp2);
    }
    
    fclose(fp1);
    fclose(fp2);
    
    if(found == 1){
        remove("issue.txt");
        rename("temp.txt", "issue.txt");
        printf("\n\n✓ Book Returned Successfully!");
    }else{
        remove("temp.txt");
        printf("\n\nRecord Not Found!");
        printf("\nEither book is not issued or already returned!");
    }
}
//fumction to show all issue book
void issuelist(){
    system("cls");
    printf("~~~Book Issue List ~~~\n");
    printf("%-10s %-30s %-20s %-10s %-30s %-12s %s\n\n", 
           "S.id", "Name", "Class", "Roll", "Book Name", "Issue Date", "Return Date");

    fp = fopen("issue.txt", "rb");
    if(fp == NULL){
        printf("No issued books found!\n");
        return;
    }
    
    while(fread(&s, sizeof(s), 1, fp) == 1){
        printf("%-10d %-30s %-20s %-10d %-30s %-12s %s\n", 
               s.id, s.sName, s.sClass, s.sRoll, s.bookName, s.date, s.returnDate);
    }

    fclose(fp);
}
//main body of code
int main() {
    int choice;
    
    while(1) {
        printf("========================================\n");
        printf("      LIBRARY MANAGEMENT SYSTEM\n");
        printf("        GOVT. GRADUATE COLLEGE\n           Qila Didar Singh\n");
        printf("========================================\n");
        printf("1. Add Book\n");
        printf("2. Display All Books\n");
        printf("3. Search Book\n");
        printf("4. Issue Book\n");
        printf("5. Return Book\n");
        printf("6. Display All Issued Books\n");
        printf("7. Exit\n");
        printf("========================================\n");
        printf("Enter your choice (1-7): ");
        scanf("%d", &choice);
        getchar();
        
        switch(choice) {
            case 1:
                addbook();
                break;
            case 2:
                booklist();
                break;
            case 3:
                searchbook();  
                break;
            case 4:
                issuebook();
                break;
            case 5:
                returnbook();
                break;
            case 6:
                issuelist();
                break;
            case 7:
                printf("\n========================================\n");
                printf("     Thanks for using the system!\n");
                printf("     From Abdullah Ehsan\n");
                printf("     Goodbye! Have a great day!\n");
                printf("========================================\n");
                return 0;
            default:
                printf("\nInvalid choice! Try again.\n");
        }
        
        printf("\nPress any key to continue...");
        getch();
    }
    
    return 0;
}