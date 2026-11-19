# ST10518855part3POE
/*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 */
package com.mycompany.main;

import java.util.Scanner;
import java.util.Arrays;
import java.util.Random;
import java.util.List;
import java.util.ArrayList;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.charset.StandardCharsets;


public class Main {

    public static void main(String[] args) {

        Scanner scanner = new Scanner(System.in);
        
        // load previoisly stored message
        Message.loadStoredMessages();

        System.out.println("========================");
        System.out.println("ChitChat Registration");
        System.out.println("========================");

        System.out.print("Enter your first name: ");
        String firstName = scanner.nextLine();

        System.out.print("Enter your last name: ");
        String lastName = scanner.nextLine();

        System.out.print("Enter a username: ");
        String username = scanner.nextLine();

        System.out.print("Enter a password: ");
        String password = scanner.nextLine();

        System.out.print("Enter your South African phone number (+27...): ");
        String cellPhoneNumber = scanner.nextLine();

        Login login = new Login(
                username, password, cellPhoneNumber,
                firstName, lastName
        );

        String registrationResult = login.registerUser();
        System.out.println(registrationResult);

        if (!registrationResult.equals("Registration successful.")) {
            System.out.println("Registration failed.");
            scanner.close();
            return;
        }

        // Login
        System.out.println("\n========================");
        System.out.println("LOGIN");
        System.out.println("========================");

        System.out.print("Enter your username: ");
        String enteredUsername = scanner.nextLine();

        System.out.print("Enter your password: ");
        String enteredPassword = scanner.nextLine();

        System.out.println(login.returnLoginStatus(
                enteredUsername, enteredPassword
        ));

        if (!login.loginUser(enteredUsername, enteredPassword)) {
            scanner.close();
            return;
        }
        
        //set sender cellphone number
        Message.setSenderNumber(cellPhoneNumber);

        System.out.println("\nWelcome to ChitChat.");

        // Ask for the message limit
        int requiredMessages;

        while (true) {
            System.out.print("How many messages would you like to send? ");

            try {
                requiredMessages = Integer.parseInt(scanner.nextLine());

                if (requiredMessages >= 0) {
                    break;
                }

                System.out.println(
                        "Please enter zero or a positive number."
                );

            } catch (NumberFormatException e) {
                System.out.println(
                        "Please enter a valid whole number."
                );
            }
        }

        boolean running = true;

        // Main menu
        while (running) {

            System.out.println("\n========================");
            System.out.println("ChitChat Menu");
            System.out.println("========================");
            System.out.println("1. Send Messages");
            System.out.println("2. Show recently sent messages");
            System.out.println("3. Quit");
            System.out.print("Choose an option: ");

            String choice = scanner.nextLine();

            switch (choice) {

                case "1":
                            
                    if (Message.returnTotalMessages()
                            >= requiredMessages) {
                        System.out.println(
                                "You have reached your message limit."
                        );
                        break;
                    }

                    System.out.print(
                            "Enter recipient cellphone number (+27...): "
                    );
                    String recipient = scanner.nextLine();

                    System.out.print("Enter your message: ");
                    String messageText = scanner.nextLine();

                    Message message = new Message(
                            recipient, messageText
                    );

                    if (!message.checkMessageID()) {
                        System.out.println("Invalid message ID.");
                        break;
                    }

                    String recipientResult =
                            message.checkRecipientCell();

                    System.out.println(recipientResult);

                    if (!message.isValidRecipientCell()) {
                        break;
                    }

                    if (!message.checkMessageLength()) {
                        System.out.println(
                                "Please enter a message of 250 characters or fewer."
                        );
                        break;
                    }

                    System.out.println(
                            "Message ID: " + message.getMessageID()
                    );
                    System.out.println(
                            "Message Hash: " + message.getMessageHash()
                    );

                    System.out.println("\n1. Send Message");
                    System.out.println("2. Show recently sent messages");
                    System.out.println("3. Store Message to send later");
                    System.out.println("4.  Quit");
                    System.out.print("Choose an option: ");

                    String messageChoice = scanner.nextLine();

                    String result =
                          message.processMessageChoice(messageChoice);

                    System.out.println(result);

                    if (message.wasSent()) {
                        System.out.println("\nMessage Details");
                        System.out.println("------------------------");
                        System.out.println(
                                "Message ID: " + message.getMessageID()
                        );
                        System.out.println(
                                "Message Hash: " + message.getMessageHash()
                        );
                        System.out.println(
                                "Recipient: " + message.getRecipient()
                        );
                        System.out.println(
                                "Message: " + message.getMessageText()
                        );
                    }

                    break;

                case "2":

                    System.out.println(Message.printMessages());
                    break;

                case "3":

                    boolean storedMenu = true;

                    while (storedMenu) {

                        System.out.println("\n========================");
                        System.out.println("STORED MESSAGES");
                        System.out.println("========================");

                        System.out.println(
                                "1. Display senders and recipients"
                        );

                        System.out.println(
                                "2. Display longest stored message"
                        );

                        System.out.println(
                                "3. Search by message ID"
                        );

                        System.out.println(
                                "4. Search by recipient"
                        );

                        System.out.println(
                                "5. Delete message using message hash"
                        );

                        System.out.println(
                                "6. Display message report"
                        );

                        System.out.println(
                                "7. Return to main menu"
                        );

                        System.out.print("Choose an option: ");

                        String storedChoice = scanner.nextLine();

                        switch (storedChoice) {

                            case "1":

                                System.out.println(
                                        Message.displaySendersAndRecipients()
                                );

                                break;

                            case "2":

                                System.out.println(
                                        Message.displayLongestMessage()
                                );

                                break;

                            case "3":

                                System.out.print("Enter message ID: ");

                                String searchID = scanner.nextLine();

                                System.out.println(
                                        Message.searchMessageByID(searchID)
                                );

                                break;

                            case "4":

                                System.out.print(
                                        "Enter recipient cellphone number: "
                                );

                                String recipientSearch = scanner.nextLine();

                                System.out.println(
                                        Message.searchMessagesByRecipient(
                                                recipientSearch
                                        )
                                );

                                break;

                            case "5":

                                System.out.print("Enter message hash: ");

                                String hash = scanner.nextLine();

                                System.out.println(
                                        Message.deleteMessageByHash(hash)
                                );

                                break;

                            case "6":

                                System.out.println(
                                        Message.displayReport()
                                );

                                break;

                            case "7":

                                storedMenu = false;
                                break;

                            default:

                                System.out.println("Invalid option.");
                        }
                    }

                    break;

                case "4":

                    running = false;

                    System.out.println(
                            "Thank you for using ChitChat."
                    );

                    break;

                default:

                    System.out.println(
                            "Invalid option. Please choose 1, 2, 3 or 4."
                    );
            }
        }
        System.out.println("\nTotal messages sent: "
                + Message.returnTotalMessages());

        scanner.close();
    }


// stored messages management menu

public static void storedMessagesMenu(Scanner scanner) {

        boolean menuRunning = true;

        while (menuRunning) {

            System.out.println("\n========================");
            System.out.println("Stored Messages Management");
            System.out.println("========================");

            System.out.println("1. Display senders and recipients");
            System.out.println("2. Display longest stored message");
            System.out.println("3. Search message by ID");
            System.out.println("4. Search messages by recipient");
            System.out.println("5. Delete message using message hash");
            System.out.println("6. Display message report");
            System.out.println("7. Return to main menu");

            System.out.print("Choose an option: ");

            String choice = scanner.nextLine();

            switch (choice) {

                case "1":

                    System.out.println(
                            Message.displaySendersAndRecipients()
                    );

                    break;

                case "2":

                    System.out.println(
                            Message.displayLongestMessage()
                    );

                    break;

                case "3":

                    System.out.print("Enter message ID: ");

                    String searchID = scanner.nextLine();

                    System.out.println(
                            Message.searchMessageByID(searchID)
                    );

                    break;

                case "4":

                    System.out.print("Enter recipient cellphone number: ");

                    String searchRecipient = scanner.nextLine();

                    System.out.println(
                            Message.searchMessagesByRecipient(
                                    searchRecipient
                            )
                    );

                    break;

                case "5":

                    System.out.print("Enter message hash: ");

                    String hash = scanner.nextLine();

                    System.out.println(
                            Message.deleteMessageByHash(hash)
                    );

                    break;

                case "6":

                    System.out.println(
                            Message.displayReport()
                    );

                    break;

                case "7":

                    menuRunning = false;
                    break;

                default:

                    System.out.println(
                            "Invalid option. Please try again."
                    );
            }
        }
    }
}



  


// =====================================================
// Login class
// =====================================================

class Login {

    private String username;
    private String password;
    private String cellPhoneNumber;
    private String firstName;
    private String lastName;

    // Constructor
    public Login(
            String username,
            String password,
            String cellPhoneNumber,
            String firstName,
            String lastName) {

        this.username = username;
        this.password = password;
        this.cellPhoneNumber = cellPhoneNumber;
        this.firstName = firstName;
        this.lastName = lastName;
    }

    // Check if username contains an underscore
    // and is no more than 5 characters long
    public boolean checkUserName() {

        return username != null
                && username.contains("_")
                && username.length() <= 5;
    }

  
    // Check password complexity
    public boolean checkPasswordComplexity() {

        boolean hasCapital = false;
        boolean hasNumber = false;
        boolean hasSpecial = false;

        // Password must have at least 8 characters
        if (password.length() < 8) {
            return false;
        }

        // Check every character in the password
        for (int i = 0; i < password.length(); i++) {

            char character = password.charAt(i);

            // Check for uppercase letter
            if (Character.isUpperCase(character)) {
                hasCapital = true;
            }

            // Check for number
            if (Character.isDigit(character)) {
                hasNumber = true;
            }

            // Check for special character
            if (!Character.isLetterOrDigit(character)) {
                hasSpecial = true;
            }
        }

        return hasCapital && hasNumber && hasSpecial;
    }

    // Check South African cellphone number
    public boolean checkCellPhoneNumber() {

        String regex = "^\\+27[6-8][0-9]{8}$";

        return cellPhoneNumber != null
                && cellPhoneNumber.matches(regex);
    }

    // Register user
    public String registerUser() {

        if (!checkUserName()) {

            return "Username is not correctly formatted; "
                    + "please ensure that your username contains "
                    + "an underscore and is no more than five "
                    + "characters in length.";
        }

        if (!checkPasswordComplexity()) {

            return "Password is not correctly formatted; "
                    + "please ensure that the password contains "
                    + "at least 8 characters, a capital letter, "
                    + "a number, and a special character.";
        }

        if (!checkCellPhoneNumber()) {

            return "Cell phone number is incorrectly formatted "
                    + "or does not contain the correct "
                    + "international code.";
        }

        return "Registration successful.";
    }

    // Check username and password during login
    public boolean loginUser(
            String enteredUsername,
            String enteredPassword) {

        return enteredUsername.equals(username)
                && enteredPassword.equals(password);
    }

    // Return login status
    public String returnLoginStatus(
            String enteredUsername,
            String enteredPassword) {

        if (loginUser(enteredUsername, enteredPassword)) {

            return "Welcome "
                    + firstName
                    + " "
                    + lastName
                    + ", it is great to see you again.";

        } else {

            return "Username or password incorrect, "
                    + "please try again.";
        }
    }
}
 
// ========================================
// Message Class 
// ========================================

class Message {

    private String messageID;
    private String recipient;
    private String sender;
    private String messageText;
    private String messageHash;

    private boolean sent;
    private boolean stored;

    private int messageNumber;

    private static int totalMessagesSent = 0;

    private static final int INITIAL_CAPACITY = 10;

    private static final Random RANDOM = new Random();

    private static String senderNumber = "";

    // FIVE REQUIRED ARRAYS

    private static Message[] sentMessages =
            new Message[INITIAL_CAPACITY];

    private static Message[] disregardedMessages =
            new Message[INITIAL_CAPACITY];

    private static Message[] storedMessages =
            new Message[INITIAL_CAPACITY];

    private static String[] messageHashes =
            new String[INITIAL_CAPACITY];

    private static String[] messageIDs =
            new String[INITIAL_CAPACITY];

    private static int sentCount = 0;
    private static int disregardedCount = 0;
    private static int storedCount = 0;
    private static int messageCount = 0;

    private static final Path JSON_PATH =
            Path.of("messages.json");

    // Constructor

    public Message(String recipient, String messageText) {

        this.messageID = generateMessageID();
        this.recipient = recipient;
        this.sender = senderNumber;
        this.messageText = messageText;

        this.sent = false;
        this.stored = false;

        this.messageNumber = messageCount + 1;

        this.messageHash = createMessageHash();

        addMessageDetailsToArrays();
    }

    // Empty constructor for loading JSON messages

    private Message() {
    }

    // Set sender cellphone number

    public static void setSenderNumber(String number) {
        senderNumber = number;
    }

    // Generate random ten-digit message ID

    private String generateMessageID() {

        long number = 1_000_000_000L
                + (long) (RANDOM.nextDouble()
                * 9_000_000_000L);

        return String.valueOf(number);
    }

    // Check message ID

    public boolean checkMessageID() {

        return messageID != null
                && messageID.matches("[0-9]{10}");
    }

    // Validate recipient cellphone number

    public String checkRecipientCell() {

        if (isValidRecipientCell()) {
            return "Cell phone number successfully captured.";
        }

        return "Cell phone number is incorrectly formatted. "
                + "Please include the South African international code (+27).";
    }

    public boolean isValidRecipientCell() {

        String regex = "^\\+27[6-8][0-9]{8}$";

        return recipient != null
                && recipient.matches(regex);
    }

    // Check message length

    public boolean checkMessageLength() {

        return messageText != null
                && messageText.length() <= 250;
    }

    // Generate message hash

    public String createMessageHash() {

        String firstWord = "";
        String lastWord = "";

        if (messageText != null
                && !messageText.trim().isEmpty()) {

            String[] words = messageText.trim().split("\\s+");

            firstWord = words[0];
            lastWord = words[words.length - 1];
        }

        String firstTwoDigits = messageID.substring(0, 2);

        return (firstTwoDigits + ":"
                + messageNumber + ":"
                + firstWord + lastWord).toUpperCase();
    }

    // Add message details to arrays

    private void addMessageDetailsToArrays() {

        if (messageCount == messageIDs.length) {

            messageIDs = Arrays.copyOf(
                    messageIDs, messageIDs.length * 2
            );

            messageHashes = Arrays.copyOf(
                    messageHashes, messageHashes.length * 2
            );
        }

        messageIDs[messageCount] = messageID;
        messageHashes[messageCount] = messageHash;

        messageCount++;
    }

    // Add sent message

    private static void addSentMessage(Message message) {

        if (sentCount == sentMessages.length) {

            sentMessages = Arrays.copyOf(
                    sentMessages, sentMessages.length * 2
            );
        }

        sentMessages[sentCount++] = message;
    }

    // Add disregarded message

    private static void addDisregardedMessage(Message message) {

        if (disregardedCount == disregardedMessages.length) {

            disregardedMessages = Arrays.copyOf(
                    disregardedMessages, disregardedMessages.length * 2
            );
        }

        disregardedMessages[disregardedCount++] = message;
    }

    // Add stored message

    private static void addStoredMessage(Message message) {

        if (storedCount == storedMessages.length) {

            storedMessages = Arrays.copyOf(
                    storedMessages, storedMessages.length * 2
            );
        }

        storedMessages[storedCount++] = message;
    }

    // Process message choice

    public String processMessageChoice(String choice) {

        switch (choice) {

            case "1":

                sent = true;
                stored = false;

                addSentMessage(this);

                totalMessagesSent++;

                return "Message successfully sent.";

            case "2":

                sent = false;
                stored = false;

                addDisregardedMessage(this);

                return "Message disregarded.";

            case "3":

                sent = false;
                stored = true;

                addStoredMessage(this);

                if (saveStoredMessages()) {
                    return "Message successfully stored.";
                }

                storedMessages[--storedCount] = null;
                stored = false;

                return "Message could not be stored.";

            default:

                return "Invalid message option.";
        }
    }

// Escape special JSON characters

private static String escapeJSON(String text) {

    if (text == null) {
        return "";
    }

    return text.replace("\\", "\\\\")
            .replace("\"", "\\\"")
            .replace("\n", "\\n")
            .replace("\r", "\\r")
            .replace("\t", "\\t")
            .replace("\b", "\\b")
            .replace("\f", "\\f");
}

// Save stored messages to JSON

public static boolean saveStoredMessages() {

    try {

        StringBuilder json = new StringBuilder();

        json.append("[\n");

        for (int i = 0; i < storedCount; i++) {

            Message message = storedMessages[i];

            json.append("  {\n");

            json.append("    \"messageID\": \"")
                    .append(escapeJSON(message.messageID))
                    .append("\",\n");

            json.append("    \"messageHash\": \"")
                    .append(escapeJSON(message.messageHash))
                    .append("\",\n");

            json.append("    \"sender\": \"")
                    .append(escapeJSON(message.sender))
                    .append("\",\n");

            json.append("    \"recipient\": \"")
                    .append(escapeJSON(message.recipient))
                    .append("\",\n");

            json.append("    \"message\": \"")
                    .append(escapeJSON(message.messageText))
                    .append("\"\n");

            json.append("  }");

            if (i < storedCount - 1) {
                json.append(",");
            }

            json.append("\n");
        }

        json.append("]");

        Files.writeString(
                JSON_PATH,
                json.toString(),
                StandardCharsets.UTF_8
        );

        return true;

    } catch (IOException e) {

        System.out.println(
                "Error storing messages: " + e.getMessage()
        );

        return false;
    }
}

// Load stored messages from JSON

public static void loadStoredMessages() {

    if (!Files.exists(JSON_PATH)) {
        return;
    }

    try {

        String json = Files.readString(
                JSON_PATH,
                StandardCharsets.UTF_8
        );

        if (json.trim().isEmpty()) {
            return;
        }

        List<String[]> records = parseJSON(json);

        storedMessages = new Message[
                Math.max(INITIAL_CAPACITY, records.size())
        ];

        storedCount = 0;

        for (String[] record : records) {

            Message message = new Message();

            message.messageID = record[0];
            message.messageHash = record[1];
            message.sender = record[2];
            message.recipient = record[3];
            message.messageText = record[4];

            message.sent = false;
            message.stored = true;

            message.messageNumber = storedCount + 1;

            addStoredMessage(message);
        }

        rebuildMessageDetailsArrays();

    } catch (IOException | IllegalArgumentException e) {

        System.out.println(
                "Error loading stored messages: " + e.getMessage()
        );
    }
}

// Rebuild message details arrays

private static void rebuildMessageDetailsArrays() {

    messageIDs = new String[INITIAL_CAPACITY];
    messageHashes = new String[INITIAL_CAPACITY];

    messageCount = 0;

    for (int i = 0; i < sentCount; i++) {
        addExistingDetails(sentMessages[i]);
    }

    for (int i = 0; i < disregardedCount; i++) {
        addExistingDetails(disregardedMessages[i]);
    }

    for (int i = 0; i < storedCount; i++) {
        addExistingDetails(storedMessages[i]);
    }
}

private static void addExistingDetails(Message message) {

    if (messageCount == messageIDs.length) {

        messageIDs = Arrays.copyOf(
                messageIDs, messageIDs.length * 2
        );

        messageHashes = Arrays.copyOf(
                messageHashes, messageHashes.length * 2
        );
    }

    messageIDs[messageCount] = message.messageID;
    messageHashes[messageCount] = message.messageHash;

    messageCount++;
}

// Manual JSON parser

private static List<String[]> parseJSON(String json) {

    List<String[]> records = new ArrayList<>();

    int position = 0;

    while (position < json.length()
            && Character.isWhitespace(json.charAt(position))) {
        position++;
    }

    if (position >= json.length()
            || json.charAt(position) != '[') {
        throw new IllegalArgumentException("Invalid JSON array.");
    }

    position++;

    while (true) {

        while (position < json.length()
                && (Character.isWhitespace(json.charAt(position))
                || json.charAt(position) == ',')) {
            position++;
        }

        if (position >= json.length()) {
            throw new IllegalArgumentException("Incomplete JSON.");
        }

        if (json.charAt(position) == ']') {
            position++;
            break;
        }

        if (json.charAt(position) != '{') {
            throw new IllegalArgumentException("Invalid JSON object.");
        }

        position++;

        String[] record = new String[5];

        while (true) {

            while (position < json.length()
                    && (Character.isWhitespace(json.charAt(position))
                    || json.charAt(position) == ',')) {
                position++;
            }

            if (position >= json.length()) {
                throw new IllegalArgumentException(
                        "Incomplete JSON object."
                );
            }

            if (json.charAt(position) == '}') {
                position++;
                break;
            }

            StringResult keyResult =
                    readJSONString(json, position);

            String key = keyResult.value;
            position = keyResult.nextPosition;

            while (position < json.length()
                    && Character.isWhitespace(json.charAt(position))) {
                position++;
            }

            if (position >= json.length()
                    || json.charAt(position) != ':') {
                throw new IllegalArgumentException(
                        "Missing JSON colon."
                );
            }

            position++;

            while (position < json.length()
                    && Character.isWhitespace(json.charAt(position))) {
                position++;
            }

            StringResult valueResult =
                    readJSONString(json, position);

            String value = valueResult.value;
            position = valueResult.nextPosition;

            switch (key) {

                case "messageID":
                    record[0] = value;
                    break;

                case "messageHash":
                    record[1] = value;
                    break;

                case "sender":
                    record[2] = value;
                    break;

                case "recipient":
                    record[3] = value;
                    break;

                case "message":
                    record[4] = value;
                    break;
            }
        }

        for (String value : record) {

            if (value == null) {
                throw new IllegalArgumentException(
                        "Missing message information in JSON."
                );
            }
        }

        records.add(record);
    }

    while (position < json.length()
            && Character.isWhitespace(json.charAt(position))) {
        position++;
    }

    if (position != json.length()) {
        throw new IllegalArgumentException(
                "Unexpected JSON content."
        );
    }

    return records;
}

// Read JSON string

private static StringResult readJSONString(
        String json, int position) {

    if (position >= json.length()
            || json.charAt(position) != '"') {
        throw new IllegalArgumentException(
                "Expected JSON string."
        );
    }

    position++;

    StringBuilder value = new StringBuilder();

    while (position < json.length()) {

        char character = json.charAt(position++);

        if (character == '"') {

            return new StringResult(
                    value.toString(), position
            );
        }

        if (character == '\\') {

            if (position >= json.length()) {
                throw new IllegalArgumentException(
                        "Invalid JSON escape."
                );
            }

            char escaped = json.charAt(position++);

            switch (escaped) {

                case '"':
                    value.append('"');
                    break;

                case '\\':
                    value.append('\\');
                    break;

                case '/':
                    value.append('/');
                    break;

                case 'n':
                    value.append('\n');
                    break;

                case 'r':
                    value.append('\r');
                    break;

                case 't':
                    value.append('\t');
                    break;

                case 'b':
                    value.append('\b');
                    break;

                case 'f':
                    value.append('\f');
                    break;

                case 'u':

                    if (position + 4 > json.length()) {
                        throw new IllegalArgumentException(
                                "Invalid Unicode escape."
                        );
                    }

                    String hex = json.substring(
                            position, position + 4
                    );

                    try {

                        value.append(
                                (char) Integer.parseInt(hex, 16)
                        );

                    } catch (NumberFormatException e) {

                        throw new IllegalArgumentException(
                                "Invalid Unicode escape."
                        );
                    }

                    position += 4;
                    break;

                default:
                    throw new IllegalArgumentException(
                            "Invalid JSON escape."
                    );
            }

        } else {

            if (character < 0x20) {
                throw new IllegalArgumentException(
                        "Invalid control character."
                );
            }

            value.append(character);
        }
    }

    throw new IllegalArgumentException(
            "Unterminated JSON string."
    );
}

// Helper class for JSON parsing

private static class StringResult {

    String value;
    int nextPosition;

    StringResult(String value, int nextPosition) {

        this.value = value;
        this.nextPosition = nextPosition;
    }
}

// OPTION 1: Display senders and recipients

public static String displaySendersAndRecipients() {

    if (storedCount == 0) {
        return "No stored messages available.";
    }

    StringBuilder output = new StringBuilder();

    for (int i = 0; i < storedCount; i++) {

        Message message = storedMessages[i];

        output.append("Sender: ")
                .append(message.sender)
                .append("\n");

        output.append("Recipient: ")
                .append(message.recipient)
                .append("\n");

        output.append("------------------------\n");
    }

    return output.toString();
}

// OPTION 2: Display longest stored message

public static String displayLongestMessage() {

    if (storedCount == 0) {
        return "No stored messages available.";
    }

    Message longest = storedMessages[0];

    for (int i = 1; i < storedCount; i++) {

        if (storedMessages[i].messageText.length()
                > longest.messageText.length()) {

            longest = storedMessages[i];
        }
    }

    return "Longest stored message:\n"
            + longest.messageText;
}

// OPTION 3: Search message by ID

public static String searchMessageByID(String searchID) {

    for (int i = 0; i < storedCount; i++) {

        Message message = storedMessages[i];

        if (message.messageID.equals(searchID)) {

            return "Message ID: " + message.messageID
                    + "\nMessage Hash: " + message.messageHash
                    + "\nRecipient: " + message.recipient
                    + "\nMessage: " + message.messageText;
        }
    }

    for (int i = 0; i < sentCount; i++) {

        Message message = sentMessages[i];

        if (message.messageID.equals(searchID)) {

            return "Message ID: " + message.messageID
                    + "\nMessage Hash: " + message.messageHash
                    + "\nRecipient: " + message.recipient
                    + "\nMessage: " + message.messageText;
        }
    }

    for (int i = 0; i < disregardedCount; i++) {

        Message message = disregardedMessages[i];

        if (message.messageID.equals(searchID)) {

            return "Message ID: " + message.messageID
                    + "\nMessage Hash: " + message.messageHash
                    + "\nRecipient: " + message.recipient
                    + "\nMessage: " + message.messageText;
        }
    }

    return "Message ID not found.";
}

// OPTION 4: Search messages by recipient

public static String searchMessagesByRecipient(String recipient) {

    StringBuilder output = new StringBuilder();

    boolean found = false;

    for (int i = 0; i < sentCount; i++) {

        Message message = sentMessages[i];

        if (message.recipient.equals(recipient)) {

            output.append("Message: ")
                    .append(message.messageText)
                    .append("\n");

            found = true;
        }
    }

    for (int i = 0; i < storedCount; i++) {

        Message message = storedMessages[i];

        if (message.recipient.equals(recipient)) {

            output.append("Message: ")
                    .append(message.messageText)
                    .append("\n");

            found = true;
        }
    }

    if (!found) {
        return "No messages found for this recipient.";
    }

    return output.toString();
}

// OPTION 5: Delete message using hash

public static String deleteMessageByHash(String hash) {

    for (int i = 0; i < storedCount; i++) {

        Message message = storedMessages[i];

        if (message.messageHash.equals(hash)) {

            String deletedMessage = message.messageText;

            Message[] previousMessages =
                    Arrays.copyOf(storedMessages, storedCount);

            int previousCount = storedCount;

            for (int j = i; j < storedCount - 1; j++) {

                storedMessages[j] = storedMessages[j + 1];
            }

            storedMessages[--storedCount] = null;

            if (!saveStoredMessages()) {

                storedMessages = previousMessages;
                storedCount = previousCount;

                return "Message could not be deleted because "
                        + "the updated file could not be saved.";
            }

            rebuildMessageDetailsArrays();

            return "Message: \"" + deletedMessage
                    + "\" successfully deleted.";
        }
    }

    return "Message hash not found.";
}

// OPTION 6: Display message report

public static String displayReport() {

    if (sentCount == 0 && storedCount == 0) {

        return "No sent or stored messages available.";
    }

    StringBuilder report = new StringBuilder();

    report.append("CHITCHAT MESSAGE REPORT\n");
    report.append("========================\n");

    for (int i = 0; i < sentCount; i++) {

        report.append("\nSTATUS: SENT\n");

        appendMessageDetails(report, sentMessages[i]);
    }

    for (int i = 0; i < storedCount; i++) {

        report.append("\nSTATUS: STORED\n");

        appendMessageDetails(report, storedMessages[i]);
    }

    return report.toString();
}

// Append message details

private static void appendMessageDetails(
        StringBuilder report,
        Message message) {

    report.append("Message ID: ")
            .append(message.messageID)
            .append("\n");

    report.append("Message Hash: ")
            .append(message.messageHash)
            .append("\n");

    report.append("Sender: ")
            .append(message.sender)
            .append("\n");

    report.append("Recipient: ")
            .append(message.recipient)
            .append("\n");

    report.append("Message: ")
            .append(message.messageText)
            .append("\n");

    report.append("------------------------\n");
}

// Display sent messages

public static String printMessages() {

    if (sentCount == 0) {
        return "No messages have been sent.";
    }

    StringBuilder output = new StringBuilder();

    for (int i = 0; i < sentCount; i++) {

        appendMessageDetails(output, sentMessages[i]);
    }

    return output.toString();
}

// Return total messages sent

public static int returnTotalMessages() {

    return totalMessagesSent;
}

// GETTERS

public String getMessageID() {
    return messageID;
}

public String getRecipient() {
    return recipient;
}

public String getSender() {
    return sender;
}

public String getMessageText() {
    return messageText;
}

public String getMessageHash() {
    return messageHash;
}

public boolean wasSent() {
    return sent;
}

public boolean wasStored() {
    return stored;
}
}











    








