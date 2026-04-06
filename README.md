#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_STUDENTS 100

// Student structure
struct Student {
    int id;
    char name[50];
    int age;
    char course[30];
    float gpa;
};

// Global array to store students
struct Student students[MAX_STUDENTS];
int totalStudents = 0;

// Function prototypes
void addStudent();
void displayAllStudents();
void searchStudent();
void updateStudent();
void deleteStudent();
void displayStudent(struct Student s);
int findStudentById(int id);

int main() {
    int choice;
    
    printf("\n====================================\n");
    printf("    STUDENT RECORD MANAGEMENT SYSTEM\n");
    printf("====================================\n");
    
    while (1) {
        printf("\n--- MAIN MENU ---\n");
        printf("1. Add Student\n");
        printf("2. Display All Students\n");
        printf("3. Search Student\n");
        printf("4. Update Student\n");
        printf("5. Delete Student\n");
        printf("6. Exit\n");
        printf("Enter your choice: ");
        
        scanf("%d", &choice);
        getchar(); // Clear input buffer
        
        switch (choice) {
            case 1:
                addStudent();
                break;
            case 2:
                displayAllStudents();
                break;
            case 3:
                searchStudent();
                break;
            case 4:
                updateStudent();
                break;
            case 5:
                deleteStudent();
                break;
            case 6:
                printf("\nThank you for using Student Record Management System!\n");
                exit(0);
            default:
                printf("Invalid choice! Please try again.\n");
        }
    }
    
    return 0;
}

// Add new student
void addStudent() {
    if (totalStudents >= MAX_STUDENTS) {
        printf("Maximum student limit reached!\n");
        return;
    }
    
    struct Student newStudent;
    newStudent.id = totalStudents + 1;
    
    printf("\n--- ADD NEW STUDENT ---\n");
    printf("Student ID: %d\n", newStudent.id);
    
    printf("Enter Name: ");
    fgets(newStudent.name, 50, stdin);
    newStudent.name[strcspn(newStudent.name, "\n")] = 0; // Remove newline
    
    printf("Enter Age: ");
    scanf("%d", &newStudent.age);
    
    printf("Enter Course: ");
    getchar(); // Clear buffer
    fgets(newStudent.course, 30, stdin);
    newStudent.course[strcspn(newStudent.course, "\n")] = 0;
    
    printf("Enter GPA: ");
    scanf("%f", &newStudent.gpa);
    
    students[totalStudents] = newStudent;
    totalStudents++;
    
    printf("Student added successfully!\n");
}

// Display all students
void displayAllStudents() {
    if (totalStudents == 0) {
        printf("No students found!\n");
        return;
    }
    
    printf("\n--- ALL STUDENTS (%d) ---\n", totalStudents);
    printf("%-5s %-20s %-5s %-15s %-8s\n", "ID", "NAME", "AGE", "COURSE", "GPA");
    printf("----------------------------------------------------\n");
    
    for (int i = 0; i < totalStudents; i++) {
        displayStudent(students[i]);
    }
}

// Search student by ID
void searchStudent() {
    int id;
    printf("Enter Student ID to search: ");
    scanf("%d", &id);
    
    int index = findStudentById(id);
    if (index != -1) {
        printf("\n--- STUDENT FOUND ---\n");
        displayStudent(students[index]);
    } else {
        printf("Student with ID %d not found!\n", id);
    }
}

// Update student record
void updateStudent() {
    int id;
    printf("Enter Student ID to update: ");
    scanf("%d", &id);
    
    int index = findStudentById(id);
    if (index == -1) {
        printf("Student with ID %d not found!\n", id);
        return;
    }
    
    printf("\n--- UPDATE STUDENT ---\n");
    printf("Current details:\n");
    displayStudent(students[index]);
    
    printf("\nEnter new details (press Enter to keep current value):\n");
    
    char choice;
    printf("Update Name? (y/n): ");
    scanf(" %c", &choice);
    if (choice == 'y' || choice == 'Y') {
        printf("Enter new Name: ");
        getchar();
        fgets(students[index].name, 50, stdin);
        students[index].name[strcspn(students[index].name, "\n")] = 0;
    }
    
    printf("Update Age? (y/n): ");
    scanf(" %c", &choice);
    if (choice == 'y' || choice == 'Y') {
        printf("Enter new Age: ");
        scanf("%d", &students[index].age);
    }
    
    printf("Update Course? (y/n): ");
    scanf(" %c", &choice);
    if (choice == 'y' || choice == 'Y') {
        printf("Enter new Course: ");
        getchar();
        fgets(students[index].course, 30, stdin);
        students[index].course[strcspn(students[index].course, "\n")] = 0;
    }
    
    printf("Update GPA? (y/n): ");
    scanf(" %c", &choice);
    if (choice == 'y' || choice == 'Y') {
        printf("Enter new GPA: ");
        scanf("%f", &students[index].gpa);
    }
    
    printf("Student updated successfully!\n");
}

// Delete student
void deleteStudent() {
    int id;
    printf("Enter Student ID to delete: ");
    scanf("%d", &id);
    
    int index = findStudentById(id);
    if (index == -1) {
        printf("Student with ID %d not found!\n", id);
        return;
    }
    
    printf("Student Found:\n");
    displayStudent(students[index]);
    printf("Are you sure to delete? (y/n): ");
    char confirm;
    scanf(" %c", &confirm);
    
    if (confirm == 'y' || confirm == 'Y') {
        // Shift all students after this index
        for (int i = index; i < totalStudents - 1; i++) {
            students[i] = students[i + 1];
        }
        totalStudents--;
        printf("Student deleted successfully!\n");
    } else {
        printf("Deletion cancelled!\n");
    }
}

// Display single student in formatted way
void displayStudent(struct Student s) {
    printf("%-5d %-20s %-5d %-15s %-8.2f\n", 
           s.id, s.name, s.age, s.course, s.gpa);
}

// Find student by ID
int findStudentById(int id) {
    for (int i = 0; i < totalStudents; i++) {
        if (students[i].id == id) {
            return i;
        }
    }
    return -1;
}
