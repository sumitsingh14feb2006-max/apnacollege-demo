#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_STUDENTS 100
#define FILENAME "students.dat"


typedef struct {
    int id;
    char name[50];
    int age;
    char course[30];
    float gpa;
} Student;

// Global variables
Student students[MAX_STUDENTS];
int totalStudents = 0;

// Function prototypes
void loadData();
void saveData();
void addStudent();
void displayAll();
void searchStudent();
void updateStudent();
void deleteStudent();
void displayStudent(Student s);
int findStudent(int id);
void clearInputBuffer();

int main() {
    loadData(); // Load existing data
    
    int choice;
    
    printf("\n============================\n");
    printf(" STUDENT RECORD SYSTEM\n");
    printf("============================\n");
    
    while (1) {
        printf("\n=== MAIN MENU ===\n");
        printf("1. Add Student\n");
        printf("2. Display All Students\n");
        printf("3. Search Student\n");
        printf("4. Update Student\n");
        printf("5. Delete Student\n");
        printf("6. Exit\n");
        printf("Enter choice: ");
        
        if (scanf("%d", &choice) != 1) {
            printf("Invalid input! Try again.\n");
            clearInputBuffer();
            continue;
        }
        clearInputBuffer();
        
        switch (choice) {
            case 1: addStudent(); break;
            case 2: displayAll(); break;
            case 3: searchStudent(); break;
            case 4: updateStudent(); break;
            case 5: deleteStudent(); break;
            case 6: 
                saveData();
                printf("\nData saved. Goodbye!\n");
                exit(0);
            default: 
                printf("Invalid choice! Enter 1-6.\n");
        }
    }
    return 0;
}

// Load data from file
void loadData() {
    FILE *file = fopen(FILENAME, "rb");
    if (file == NULL) {
        printf("No existing data file found. Starting fresh.\n");
        totalStudents = 0;
        return;
    }
    
    fread(&totalStudents, sizeof(int), 1, file);
    fread(students, sizeof(Student), totalStudents, file);
    fclose(file);
    printf("Loaded %d students from file.\n", totalStudents);
}

// Save data to file
void saveData() {
    FILE *file = fopen(FILENAME, "wb");
    if (file == NULL) {
        printf("Error saving data!\n");
        return;
    }
    
    fwrite(&totalStudents, sizeof(int), 1, file);
    fwrite(students, sizeof(Student), totalStudents, file);
    fclose(file);
    printf("Data saved successfully!\n");
}

// Add new student
void addStudent() {
    if (totalStudents >= MAX_STUDENTS) {
        printf("Storage full! Maximum %d students allowed.\n", MAX_STUDENTS);
        return;
    }
    
    Student *s = &students[totalStudents];
    s->id = totalStudents + 1;
    
    printf("\n--- ADD STUDENT ---\n");
    printf("ID: %d\n", s->id);
    
    printf("Name: ");
    clearInputBuffer();
    fgets(s->name, 50, stdin);
    s->name[strcspn(s->name, "\n")] = 0;
    
    printf("Age: ");
    while (scanf("%d", &s->age) != 1 || s->age < 15 || s->age > 100) {
        printf("Invalid age! Enter 15-100: ");
        clearInputBuffer();
    }
    clearInputBuffer();
    
    printf("Course: ");
    fgets(s->course, 30, stdin);
    s->course[strcspn(s->course, "\n")] = 0;
    
    printf("GPA (0.0-4.0): ");
    while (scanf("%f", &s->gpa) != 1 || s->gpa < 0 || s->gpa > 4.0) {
        printf("Invalid GPA! Enter 0.0-4.0: ");
        clearInputBuffer();
    }
    
    totalStudents++;
    printf("✓ Student added successfully!\n");
}

// Display all students
void displayAll() {
    if (totalStudents == 0) {
        printf("No students found!\n");
        return;
    }
    
    printf("\n--- ALL STUDENTS (%d) ---\n", totalStudents);
    printf("%-4s %-20s %-4s %-15s %-6s\n", "ID", "NAME", "AGE", "COURSE", "GPA");
    printf("-------------------------------------------------\n");
    
    for (int i = 0; i < totalStudents; i++) {
        displayStudent(students[i]);
    }
}

// Search student
void searchStudent() {
    int id;
    printf("Enter Student ID: ");
    if (scanf("%d", &id) != 1) {
        printf("Invalid ID!\n");
        clearInputBuffer();
        return;
    }
    clearInputBuffer();
    
    int index = findStudent(id);
    if (index != -1) {
        printf("\n--- STUDENT FOUND ---\n");
        displayStudent(students[index]);
    } else {
        printf("Student ID %d not found!\n", id);
    }
}

// Update student
void updateStudent() {
    int id;
    printf("Enter Student ID to update: ");
    if (scanf("%d", &id) != 1) {
        printf("Invalid ID!\n");
        clearInputBuffer();
        return;
    }
    clearInputBuffer();
    
    int index = findStudent(id);
    if (index == -1) {
        printf("Student not found!\n");
        return;
    }
    
    printf("\n--- CURRENT DETAILS ---\n");
    displayStudent(students[index]);
    
    char choice;
    printf("\nUpdate Name? (y/n): ");
    scanf(" %c", &choice);
    if (choice == 'y' || choice == 'Y') {
        printf("New Name: ");
        clearInputBuffer();
        fgets(students[index].name, 50, stdin);
        students[index].name[strcspn(students[index].name, "\n")] = 0;
    }
    
    printf("Update Age? (y/n): ");
    scanf(" %c", &choice);
    if (choice == 'y' || choice == 'Y') {
        printf("New Age: ");
        scanf("%d", &students[index].age);
    }
    
    printf("Update Course? (y/n): ");
    scanf(" %c", &choice);
    if (choice == 'y' || choice == 'Y') {
        printf("New Course: ");
        clearInputBuffer();
        fgets(students[index].course, 30, stdin);
        students[index].course[strcspn(students[index].course, "\n")] = 0;
    }
    
    printf("Update GPA? (y/n): ");
    scanf(" %c", &choice);
    if (choice == 'y' || choice == 'Y') {
        printf("New GPA: ");
        scanf("%f", &students[index].gpa);
    }
    
    printf("✓ Student updated!\n");
}

// Delete student
void deleteStudent() {
    int id;
    printf("Enter Student ID to delete: ");
    scanf("%d", &id);
    clearInputBuffer();
    
    int index = findStudent(id);
    if (index == -1) {
        printf("Student not found!\n");
        return;
    }
    
    printf("\n--- TO DELETE ---\n");
    displayStudent(students[index]);
    
    char confirm;
    printf("Confirm delete? (y/n): ");
    scanf(" %c", &confirm);
    
    if (confirm == 'y' || confirm == 'Y') {
        for (int i = index; i < totalStudents - 1; i++) {
            students[i] = students[i + 1];
        }
        totalStudents--;
        printf("✓ Student deleted!\n");
    } else {
        printf("Cancelled.\n");
    }
}

// Display single student
void displayStudent(Student s) {
    printf("%-4d %-20s %-4d %-15s %-6.2f\n", 
           s.id, s.name, s.age, s.course, s.gpa);
}

// Find student by ID
int findStudent(int id) {
    for (int i = 0; i < totalStudents; i++) {
        if (students[i].id == id) return i;
    }
    return -1;
}

// Clear input buffer
void clearInputBuffer() {
    int c;
    while ((c = getchar()) != '\n' && c != EOF);
}
