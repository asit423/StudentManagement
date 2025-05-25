# StudentManagement

import java.io.*;
import java.util.*;

class Student {
    String id, name;
    int grade;

    public Student(String id, String name, int grade) {
        this.id = id;
        this.name = name;
        this.grade = grade;
    }

    public String toCSV() {
        return id + "," + name + "," + grade;
    }

    public static Student fromCSV(String csvLine) {
        String[] data = csvLine.split(",");
        return new Student(data[0], data[1], Integer.parseInt(data[2]));
    }
}

public class StudentManager {
    static final String FILE_NAME = "students.csv";

    public static void createStudent(Student student) throws IOException {
        if (findStudentById(student.id) != null) {
            System.out.println("Duplicate ID. Student already exists.");
            return;
        }

        FileWriter fw = new FileWriter(FILE_NAME, true);
        fw.write(student.toCSV() + "\n");
        fw.close();
        System.out.println("Student added successfully.");
    }

    public static Student findStudentById(String id) throws IOException {
        BufferedReader br = new BufferedReader(new FileReader(FILE_NAME));
        String line;
        while ((line = br.readLine()) != null) {
            Student s = Student.fromCSV(line);
            if (s.id.equals(id)) {
                br.close();
                return s;
            }
        }
        br.close();
        return null;
    }

    public static void readStudent(String id) throws IOException {
        Student s = findStudentById(id);
        if (s != null) {
            System.out.println("ID: " + s.id + ", Name: " + s.name + ", Grade: " + s.grade);
        } else {
            System.out.println("Student not found.");
        }
    }

    public static void updateStudent(String id, String newName, int newGrade) throws IOException {
        File inputFile = new File(FILE_NAME);
        File tempFile = new File("temp.csv");

        BufferedReader reader = new BufferedReader(new FileReader(inputFile));
        FileWriter writer = new FileWriter(tempFile);

        String line;
        boolean updated = false;

        while ((line = reader.readLine()) != null) {
            Student s = Student.fromCSV(line);
            if (s.id.equals(id)) {
                s.name = newName;
                s.grade = newGrade;
                updated = true;
            }
            writer.write(s.toCSV() + "\n");
        }

        writer.close();
        reader.close();

        if (inputFile.delete() && tempFile.renameTo(inputFile)) {
            if (updated) {
                System.out.println("Student updated successfully.");
            } else {
                System.out.println("Student ID not found.");
            }
        }
    }

    public static void deleteStudent(String id) throws IOException {
        File inputFile = new File(FILE_NAME);
        File tempFile = new File("temp.csv");

        BufferedReader reader = new BufferedReader(new FileReader(inputFile));
        FileWriter writer = new FileWriter(tempFile);

        String line;
        boolean deleted = false;

        while ((line = reader.readLine()) != null) {
            Student s = Student.fromCSV(line);
            if (s.id.equals(id)) {
                deleted = true;
                continue;
            }
            writer.write(s.toCSV() + "\n");
        }

        writer.close();
        reader.close();

        if (inputFile.delete() && tempFile.renameTo(inputFile)) {
            if (deleted) {
                System.out.println("Student deleted successfully.");
            } else {
                System.out.println("Student ID not found.");
            }
        }
    }

    public static void main(String[] args) throws IOException {
        Scanner sc = new Scanner(System.in);
        while (true) {
            System.out.println("\n1. Create\n2. Read\n3. Update\n4. Delete\n5. Exit");
            System.out.print("Choose an option: ");
            int option = sc.nextInt();
            sc.nextLine(); // consume newline

            switch (option) {
                case 1:
                    System.out.print("Enter ID, Name, Grade: ");
                    String id = sc.next();
                    String name = sc.next();
                    int grade = sc.nextInt();
                    createStudent(new Student(id, name, grade));
                    break;
                case 2:
                    System.out.print("Enter ID to read: ");
                    readStudent(sc.next());
                    break;
                case 3:
                    System.out.print("Enter ID, New Name, New Grade: ");
                    updateStudent(sc.next(), sc.next(), sc.nextInt());
                    break;
                case 4:
                    System.out.print("Enter ID to delete: ");
                    deleteStudent(sc.next());
                    break;
                case 5:
                    sc.close();
                    System.exit(0);
            }
        }
    }
}
