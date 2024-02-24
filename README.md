#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_NAME_LENGTH 50
#define MAX_PATIENTS 100
#define FILENAME "patients.txt"

struct Patient {
    char name[MAX_NAME_LENGTH];
    int age;
    char gender;
};

struct Hospital {
    struct Patient *patients;
    int count;
};

void addPatient(struct Hospital *hospital) {
    if (hospital->count < MAX_PATIENTS) {
        struct Patient newPatient;
        printf("Enter patient name: ");
        scanf("%s", newPatient.name);
        printf("Enter patient age: ");
        scanf("%d", &newPatient.age);
        printf("Enter patient gender (M/F): ");
        scanf(" %c", &newPatient.gender);
        
        hospital->patients[hospital->count] = newPatient;
        hospital->count++;
        printf("Patient added successfully.\n");
    } else {
        printf("Hospital database is full. Cannot add more patients.\n");
    }
}

void viewPatients(struct Hospital hospital) {
    printf("List of Patients:\n");
    if (hospital.count == 0) {
        printf("No patients in the database.\n");
    } else {
        for (int i = 0; i < hospital.count; i++) {
            printf("Name: %s, Age: %d, Gender: %c\n",
                hospital.patients[i].name,
                hospital.patients[i].age,
                hospital.patients[i].gender);
        }
    }
}

void searchPatient(struct Hospital hospital, char *name) {
    int found = 0;
    for (int i = 0; i < hospital.count; i++) {
        if (strcmp(hospital.patients[i].name, name) == 0) {
            printf("Patient found:\n");
            printf("Name: %s, Age: %d, Gender: %c\n",
                hospital.patients[i].name,
                hospital.patients[i].age,
                hospital.patients[i].gender);
            found = 1;
            break;
        }
    }
    if (!found) {
        printf("Patient with name '%s' not found.\n", name);
    }
}

void deletePatient(struct Hospital *hospital, char *name) {
    int found = 0;
    for (int i = 0; i < hospital->count; i++) {
        if (strcmp(hospital->patients[i].name, name) == 0) {
            found = 1;
            for (int j = i; j < hospital->count - 1; j++) {
                hospital->patients[j] = hospital->patients[j + 1];
            }
            hospital->count--;
            printf("Patient '%s' deleted successfully.\n", name);
            break;
        }
    }
    if (!found) {
        printf("Patient with name '%s' not found.\n", name);
    }
}

void sortPatients(struct Hospital *hospital) {
    int i, j;
    struct Patient temp;
    for (i = 0; i < hospital->count - 1; i++) {
        for (j = 0; j < hospital->count - i - 1; j++) {
            if (strcmp(hospital->patients[j].name, hospital->patients[j + 1].name) > 0) {
                temp = hospital->patients[j];
                hospital->patients[j] = hospital->patients[j + 1];
                hospital->patients[j + 1] = temp;
            }
        }
    }
    printf("Patients sorted successfully.\n");
}

void savePatients(struct Hospital hospital) {
    FILE *file = fopen(FILENAME, "w");
    if (file == NULL) {
        printf("Error opening file for writing.\n");
        return;
    }
    for (int i = 0; i < hospital.count; i++) {
        fprintf(file, "%s %d %c\n", hospital.patients[i].name, hospital.patients[i].age, hospital.patients[i].gender);
    }
    fclose(file);
    printf("Patients saved to file successfully.\n");
}

void loadPatients(struct Hospital *hospital) {
    FILE *file = fopen(FILENAME, "r");
    if (file == NULL) {
        printf("No existing data found.\n");
        return;
    }
    while (!feof(file)) {
        struct Patient newPatient;
        if (fscanf(file, "%s %d %c\n", newPatient.name, &newPatient.age, &newPatient.gender) != EOF) {
            hospital->patients[hospital->count] = newPatient;
            hospital->count++;
        }
    }
    fclose(file);
    printf("Patients loaded from file successfully.\n");
}

int main() {
    struct Hospital hospital;
    hospital.patients = (struct Patient *)malloc(MAX_PATIENTS * sizeof(struct Patient));
    hospital.count = 0;
    int choice;
    char name[MAX_NAME_LENGTH];

    loadPatients(&hospital);

    do {
        printf("\nHospital Management System\n");
        printf("1. Add Patient\n");
        printf("2. View Patients\n");
        printf("3. Search Patient\n");
        printf("4. Delete Patient\n");
        printf("5. Sort Patients\n");
        printf("6. Save Patients\n");
        printf("7. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch(choice) {
            case 1:
                addPatient(&hospital);
                break;
            case 2:
                viewPatients(hospital);
                break;
            case 3:
                printf("Enter patient name to search: ");
                scanf("%s", name);
                searchPatient(hospital, name);
                break;
            case 4:
                printf("Enter patient name to delete: ");
                scanf("%s", name);
                deletePatient(&hospital, name);
                break;
            case 5:
                sortPatients(&hospital);
                break;
            case 6:
                savePatients(hospital);
                break;
            case 7:
                printf("Exiting program. Goodbye!\n");
                break;
            default:
                printf("Invalid choice. Please try again.\n");
        }
    } while (choice != 7);

    free(hospital.patients);
    return 0;
}
