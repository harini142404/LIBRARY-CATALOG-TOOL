# LIBRARY-CATALOG-TOOL

import java.awt.*;
import java.io.*;
import java.util.ArrayList;
import javax.swing.*;

class Book {
    int id;
    String title;
    String author;
    boolean issued;
    Book(int id, String title, String author) {
        this.id = id;
        this.title = title;
        this.author = author;
        this.issued = false;
    }
    @Override
    public String toString() {
        return id + "," + title + "," + author + "," + issued;
    }
}
public class LibraryManagementSystem extends JFrame {
    private ArrayList<Book> books = new ArrayList<>();
    private JTextArea displayArea;
    private boolean isAdmin;
    private final String FILE_NAME = "books.txt";
    public LibraryManagementSystem(boolean isAdmin) {
        this.isAdmin = isAdmin;
        loadBooks();
        setTitle("Library Management System");
        setSize(800, 500);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout());
        JLabel title = new JLabel("Library Management System", SwingConstants.CENTER);
        title.setFont(new Font("Arial", Font.BOLD, 22));
        add(title, BorderLayout.NORTH);
        displayArea = new JTextArea();
        displayArea.setEditable(false);
        add(new JScrollPane(displayArea), BorderLayout.CENTER);
        JPanel buttonPanel = new JPanel(new GridLayout(2, 3, 10, 10));
        JButton addBtn = new JButton("Add Book");
        JButton viewBtn = new JButton("View Books");
        JButton searchBtn = new JButton("Search Book");
        JButton issueBtn = new JButton("Issue Book");
        JButton returnBtn = new JButton("Return Book");
        JButton clearBtn = new JButton("Clear");
        buttonPanel.add(addBtn);
        buttonPanel.add(viewBtn);
        buttonPanel.add(searchBtn);
        buttonPanel.add(issueBtn);
        buttonPanel.add(returnBtn);
        buttonPanel.add(clearBtn);
        add(buttonPanel, BorderLayout.SOUTH);
        if (!isAdmin) {
            addBtn.setEnabled(false);
        }
        addBtn.addActionListener(e -> addBook());
        viewBtn.addActionListener(e -> viewBooks());
        searchBtn.addActionListener(e -> searchBook());
        issueBtn.addActionListener(e -> issueBook());
        returnBtn.addActionListener(e -> returnBook());
        clearBtn.addActionListener(e -> displayArea.setText(""));
        setVisible(true);
    }
    private void addBook() {
        try {
            int id = Integer.parseInt(
                    JOptionPane.showInputDialog("Enter Book ID"));
            String title = JOptionPane.showInputDialog("Enter Book Title");
            String author = JOptionPane.showInputDialog("Enter Author");
            books.add(new Book(id, title, author));
            saveBooks();
            JOptionPane.showMessageDialog(this,
                    "Book Added Successfully");
        } catch (Exception ex) {
            JOptionPane.showMessageDialog(this,
                    "Invalid Input");
        }
    }
    private void viewBooks() {
        displayArea.setText("");
        if (books.isEmpty()) {
            displayArea.append("No books available.\n");
            return;
        }
        for (Book b : books) {
            displayArea.append(
                    "ID: " + b.id +
                    " | Title: " + b.title +
                    " | Author: " + b.author +
                    " | Status: " +
                    (b.issued ? "Issued" : "Available")
                    + "\n");
        }
    }
    private void searchBook() {
        String searchTitle =
                JOptionPane.showInputDialog("Enter Title");
        displayArea.setText("");
        boolean found = false;
        for (Book b : books) {
            if (b.title.toLowerCase()
                    .contains(searchTitle.toLowerCase())) {
                displayArea.append(
                        "ID: " + b.id +
                        " | Title: " + b.title +
                        " | Author: " + b.author +
                        " | Status: " +
                        (b.issued ? "Issued" : "Available")
                        + "\n");
                found = true;
            }
        }
        if (!found) {
            displayArea.append("Book Not Found\n");
        }
    }
    private void issueBook() {
        try {
            int id = Integer.parseInt(
                    JOptionPane.showInputDialog("Enter Book ID"));
            for (Book b : books) {
                if (b.id == id) {
                    if (!b.issued) {
                        b.issued = true;
                        saveBooks();
                        JOptionPane.showMessageDialog(
                                this,
                                "Book Issued Successfully");
                    } else {
                        JOptionPane.showMessageDialog(
                                this,
                                "Book Already Issued");
                    }
                    return;
                }
            }
            JOptionPane.showMessageDialog(
                    this,
                    "Book Not Found");
        } catch (Exception ex) {
            JOptionPane.showMessageDialog(
                    this,
                    "Invalid Input");
        }
    }
    private void returnBook() {
        try {
            int id = Integer.parseInt(
                    JOptionPane.showInputDialog("Enter Book ID"));
            int lateDays = Integer.parseInt(
                    JOptionPane.showInputDialog("Late Days"));
            double fine = lateDays * 5;
            for (Book b : books) {
                if (b.id == id) {
                    b.issued = false;
                    saveBooks();
                    JOptionPane.showMessageDialog(
                            this,
                            "Book Returned\nFine = ₹" + fine);
                    return;
                }
            }
            JOptionPane.showMessageDialog(
                    this,
                    "Book Not Found");
        } catch (Exception ex) {
            JOptionPane.showMessageDialog(
                    this,
                    "Invalid Input");
        }
    }
    private void saveBooks() {
        try (PrintWriter writer =
                     new PrintWriter(FILE_NAME)) {
            for (Book b : books) {
                writer.println(b);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    private void loadBooks() {
        File file = new File(FILE_NAME);
        if (!file.exists())
            return;
        try (BufferedReader br =
                     new BufferedReader(
                             new FileReader(file))) {
            String line;
            while ((line = br.readLine()) != null) {
                String[] data = line.split(",");
                Book book = new Book(
                        Integer.parseInt(data[0]),
                        data[1],
                        data[2]);
                book.issued =
                        Boolean.parseBoolean(data[3]);
                books.add(book);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    public static void main(String[] args) {
        String username =
                JOptionPane.showInputDialog("Username");
        String password =
                JOptionPane.showInputDialog("Password");
        if (username == null || password == null)
            return;
        if (username.equals("admin")
                && password.equals("admin123")) {
            new LibraryManagementSystem(true);
        } else if (username.equals("student")
                && password.equals("student123")) {
            new LibraryManagementSystem(false);
        } else {
            JOptionPane.showMessageDialog(
                    null,
                    "Invalid Login");
        }
    }
}
