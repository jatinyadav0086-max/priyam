
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_STUDENTS 1000
#define DATA_FILE "students.dat"
#define TEMP_FILE "students.tmp"

typedef struct {
    int uid;
    char name[60];
    char course[30];
    int semester;
    char email[70];
    float marks;
    float attendance;
} Student;

void loadStudents(Student students[], int *count) {
    FILE *fp = fopen(DATA_FILE, "rb");
    *count = 0;
    if (!fp) return;
    while (*count < MAX_STUDENTS &&
           fread(&students[*count], sizeof(Student), 1, fp) == 1) {
        (*count)++;
    }
    fclose(fp);
}

int saveStudents(Student students[], int count) {
    FILE *fp = fopen(TEMP_FILE, "wb");
    if (!fp) return 0;
    if (count > 0 && fwrite(students, sizeof(Student), count, fp) != (size_t)count) {
        fclose(fp);
        remove(TEMP_FILE);
        return 0;
    }
    fclose(fp);
    remove(DATA_FILE);
    return rename(TEMP_FILE, DATA_FILE) == 0;
}

int findStudentIndex(Student students[], int count, int uid) {
    for (int i = 0; i < count; i++)
        if (students[i].uid == uid) return i;
    return -1;
}

void readLine(const char *prompt, char *buffer, size_t size) {
    printf("%s", prompt);
    if (fgets(buffer, (int)size, stdin)) {
        buffer[strcspn(buffer, "\n")] = '\0';
    } else buffer[0] = '\0';
}

int readInt(const char *prompt) {
    char line[80];
    int value;
    for (;;) {
        readLine(prompt, line, sizeof(line));
        if (sscanf(line, "%d", &value) == 1) return value;
        printf("Please enter a valid whole number.\n");
    }
}

float readFloat(const char *prompt) {
    char line[80];
    float value;
    for (;;) {
        readLine(prompt, line, sizeof(line));
        if (sscanf(line, "%f", &value) == 1) return value;
        printf("Please enter a valid number.\n");
    }
}

void addStudent(Student students[], int *count) {
    if (*count >= MAX_STUDENTS) {
        printf("Student limit reached.\n");
        return;
    }
    Student s;
    s.uid = readInt("Enter student UID: ");
    if (s.uid <= 0 || findStudentIndex(students, *count, s.uid) != -1) {
        printf("UID must be positive and unique.\n");
        return;
    }
    readLine("Enter student name: ", s.name, sizeof(s.name));
    readLine("Enter course: ", s.course, sizeof(s.course));
    s.semester = readInt("Enter semester: ");
    readLine("Enter email: ", s.email, sizeof(s.email));
    s.marks = readFloat("Enter marks (0-100): ");
    s.attendance = readFloat("Enter attendance (0-100): ");
    if (s.semester < 1 || s.semester > 12 || s.marks < 0 || s.marks > 100 ||
        s.attendance < 0 || s.attendance > 100) {
        printf("Invalid semester, marks, or attendance value.\n");
        return;
    }
    students[(*count)++] = s;
    printf(saveStudents(students, *count) ? "Student added and saved successfully.\n"
                                         : "Save failed.\n");
}

void displayStudents(Student students[], int count) {
    if (!count) {
        printf("No student records found.\n");
        return;
    }
    printf("\nUID      NAME                 COURSE       SEM  MARKS  ATTENDANCE\n");
    printf("-----------------------------------------------------------------\n");
    for (int i = 0; i < count; i++)
        printf("%-8d %-20s %-12s %-4d %-6.1f %.1f%%\n",
               students[i].uid, students[i].name, students[i].course,
               students[i].semester, students[i].marks, students[i].attendance);
}

void searchStudent(Student students[], int count) {
    int uid = readInt("Enter student UID to search: ");
    int i = findStudentIndex(students, count, uid);
    if (i < 0) {
        printf("Student not found.\n");
        return;
    }
    printf("UID: %d | Name: %s | Course: %s | Semester: %d | Email: %s | Marks: %.1f | Attendance: %.1f%%\n",
           students[i].uid, students[i].name, students[i].course,
           students[i].semester, students[i].email, students[i].marks,
           students[i].attendance);
}

void updateMarks(Student students[], int count) {
    int uid = readInt("Enter student UID: ");
    int i = findStudentIndex(students, count, uid);
    if (i < 0) {
        printf("Student not found.\n");
        return;
    }
    float marks = readFloat("Enter new marks (0-100): ");
    if (marks < 0 || marks > 100) {
        printf("Marks must be between 0 and 100.\n");
        return;
    }
    students[i].marks = marks;
    printf(saveStudents(students, count) ? "Marks updated successfully.\n" : "Save failed.\n");
}

void updateAttendance(Student students[], int count) {
    int uid = readInt("Enter student UID: ");
    int i = findStudentIndex(students, count, uid);
    if (i < 0) {
        printf("Student not found.\n");
        return;
    }
    float attendance = readFloat("Enter attendance percentage (0-100): ");
    if (attendance < 0 || attendance > 100) {
        printf("Attendance must be between 0 and 100.\n");
        return;
    }
    students[i].attendance = attendance;
    printf(saveStudents(students, count) ? "Attendance updated successfully.\n" : "Save failed.\n");
}

void deleteStudent(Student students[], int *count) {
    int uid = readInt("Enter student UID to delete: ");
    int i = findStudentIndex(students, *count, uid);
    if (i < 0) {
        printf("Student not found.\n");
        return;
    }
    for (int j = i; j < *count - 1; j++)
        students[j] = students[j + 1];
    (*count)--;
    printf(saveStudents(students, *count) ? "Student record deleted successfully.\n" : "Save failed.\n");
}

int main(void) {
    Student students[MAX_STUDENTS];
    int count = 0, choice;
    loadStudents(students, &count);

    do {
        printf("\n========== STUDENT MANAGEMENT SYSTEM ==========\n");
        printf("1. Add New Student\n2. Display All Students\n3. Search Student by UID\n");
        printf("4. Update Student Marks\n5. Update Attendance\n6. Delete Student Record\n7. Exit\n");
        choice = readInt("Enter your choice (1-7): ");

        switch (choice) {
            case 1: addStudent(students, &count); break;
            case 2: displayStudents(students, count); break;
            case 3: searchStudent(students, count); break;
            case 4: updateMarks(students, count); break;
            case 5: updateAttendance(students, count); break;
            case 6: deleteStudent(students, &count); break;
            case 7: printf("Thank you for using Student Management System.\n"); break;
            default: printf("Invalid choice. Please select 1-7.\n");
        }
    } while (choice != 7);

    return 0;
}
