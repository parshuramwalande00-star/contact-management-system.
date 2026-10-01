# contact-management-system
#include <iostream>
#include <vector>
#include <fstream>
#include <string>
using namespace std;

struct Contact {
    string name;
    string phone;
    string email;
    string address;
};

// Display one contact
void displayContact(const Contact& c) {
    cout << "\nName    : " << c.name;
    cout << "\nPhone   : " << c.phone;
    cout << "\nEmail   : " << c.email;
    cout << "\nAddress : " << c.address << "\n";
}

// Save contacts to file
void saveContacts(const vector<Contact>& contacts) {
    ofstream file("contacts.txt");

    for (const Contact& c : contacts) {
        file << c.name << "|"
             << c.phone << "|"
             << c.email << "|"
             << c.address << "\n";
    }

    file.close();
}

// Load contacts from file
void loadContacts(vector<Contact>& contacts) {
    ifstream file("contacts.txt");

    if (!file)
        return;

    Contact c;
    string line;

    while (getline(file, line)) {
        size_t p1 = line.find('|');
        size_t p2 = line.find('|', p1 + 1);
        size_t p3 = line.find('|', p2 + 1);

        if (p1 == string::npos ||
            p2 == string::npos ||
            p3 == string::npos)
            continue;

        c.name = line.substr(0, p1);
        c.phone = line.substr(p1 + 1, p2 - p1 - 1);
        c.email = line.substr(p2 + 1, p3 - p2 - 1);
        c.address = line.substr(p3 + 1);

        contacts.push_back(c);
    }

    file.close();
}

// Add contact
void addContact(vector<Contact>& contacts) {
    Contact c;

    cin.ignore();

    cout << "\nEnter name: ";
    getline(cin, c.name);

    cout << "Enter phone: ";
    getline(cin, c.phone);

    cout << "Enter email: ";
    getline(cin, c.email);

    cout << "Enter address: ";
    getline(cin, c.address);

    contacts.push_back(c);
    saveContacts(contacts);

    cout << "\nContact added successfully!\n";
}

// Display all contacts
void displayAll(const vector<Contact>& contacts) {
    if (contacts.empty()) {
        cout << "\nNo contacts found.\n";
        return;
    }

    cout << "\n===== ALL CONTACTS =====\n";

    for (const Contact& c : contacts) {
        displayContact(c);
        cout << "------------------------\n";
    }
}

// Search contact
void searchContact(const vector<Contact>& contacts) {
    string search;
    bool found = false;

    cin.ignore();

    cout << "\nEnter name or phone to search: ";
    getline(cin, search);

    for (const Contact& c : contacts) {
        if (c.name == search || c.phone == search) {
            displayContact(c);
            found = true;
        }
    }

    if (!found)
        cout << "\nContact not found.\n";
}

// Edit contact
void editContact(vector<Contact>& contacts) {
    string name;
    bool found = false;

    cin.ignore();

    cout << "\nEnter name of contact to edit: ";
    getline(cin, name);

    for (Contact& c : contacts) {

        if (c.name == name) {
            cout << "\nEnter new phone: ";
            getline(cin, c.phone);

            cout << "Enter new email: ";
            getline(cin, c.email);

            cout << "Enter new address: ";
            getline(cin, c.address);

            found = true;
            saveContacts(contacts);

            cout << "\nContact updated successfully!\n";
            break;
        }
    }

    if (!found)
        cout << "\nContact not found.\n";
}

// Delete contact
void deleteContact(vector<Contact>& contacts) {
    string name;
    bool found = false;

    cin.ignore();

    cout << "\nEnter name of contact to delete: ";
    getline(cin, name);

    for (auto it = contacts.begin(); it != contacts.end(); ++it) {

        if (it->name == name) {
            contacts.erase(it);
            found = true;

            saveContacts(contacts);

            cout << "\nContact deleted successfully!\n";
            break;
        }
    }

    if (!found)
        cout << "\nContact not found.\n";
}

int main() {

    vector<Contact> contacts;

    // Load saved contacts when program starts
    loadContacts(contacts);

    int choice;

    do {
        cout << "\n====================================\n";
        cout << "       CONTACT MANAGEMENT SYSTEM\n";
        cout << "====================================\n";
        cout << "1. Add Contact\n";
        cout << "2. Display All Contacts\n";
        cout << "3. Search Contact\n";
        cout << "4. Edit Contact\n";
        cout << "5. Delete Contact\n";
        cout << "6. Exit\n";
        cout << "====================================\n";

        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {

        case 1:
            addContact(contacts);
            break;

        case 2:
            displayAll(contacts);
            break;

        case 3:
            searchContact(contacts);
            break;

        case 4:
            editContact(contacts);
            break;

        case 5:
            deleteContact(contacts);
            break;

        case 6:
            saveContacts(contacts);
            cout << "\nContacts saved. Thank you!\n";
            break;

        default:
            cout << "\nInvalid choice! Try again.\n";
        }

    } while (choice != 6);

    return 0;
}