import java.util.*;

class Guest { String name; int roomNumber; int days; double bill; String roomType; Date checkInTime; String paymentMethod;

Guest(String name, int roomNumber, int days, String roomType, double ratePerDay, String paymentMethod) {
    this.name = name;
    this.roomNumber = roomNumber;
    this.days = days;
    this.roomType = roomType;
    this.paymentMethod = paymentMethod;
    this.bill = days * ratePerDay;
    this.checkInTime = new Date();
}

public String toString() {
    return "Name: " + name + ", Room: " + roomNumber + " (" + roomType + "), Days: " + days +
            ", Bill: $" + bill + ", Payment: " + paymentMethod + ", Check-in: " + checkInTime;
}

}

class Employee { String name; String id; String role;

Employee(String name, String id, String role) {
    this.name = name;
    this.id = id;
    this.role = role;
}

public String toString() {
    return "Employee: " + name + ", ID: " + id + ", Role: " + role;
}

}

public class HotelManagementSystem { static Scanner scanner = new Scanner(System.in); static List<Guest> guestList = new ArrayList<>(); static List<Employee> employeeList = new ArrayList<>(); static Set<Integer> occupiedRooms = new HashSet<>(); static double acRatePerDay = 150.0; static double nonAcRatePerDay = 100.0;

public static void main(String[] args) {
    if (!adminLogin()) {
        System.out.println("Access denied.");
        return;
    }

    int choice;
    do {
        System.out.println("\n=== Hotel Management Menu ===");
        System.out.println("1. Book Room");
        System.out.println("2. View All Guests");
        System.out.println("3. Search Guest by Name");
        System.out.println("4. View Room Availability");
        System.out.println("5. Checkout Guest");
        System.out.println("6. Delete Guest by Name");
        System.out.println("7. Sort Guests by Bill (High to Low)");
        System.out.println("8. Filter Guests by Room Type");
        System.out.println("9. View Report");
        System.out.println("10. Employee Management");
        System.out.println("11. Exit");
        System.out.print("Enter your choice: ");
        choice = scanner.nextInt();
        scanner.nextLine();

        switch (choice) {
            case 1 -> bookRoom();
            case 2 -> viewGuests();
            case 3 -> searchGuest();
            case 4 -> viewAvailability();
            case 5 -> checkoutGuest();
            case 6 -> deleteGuestByName();
            case 7 -> sortGuestsByBill();
            case 8 -> filterByRoomType();
            case 9 -> viewReport();
            case 10 -> employeeManagement();
            case 11 -> System.out.println("Exiting...");
            default -> System.out.println("Invalid choice!");
        }
    } while (choice != 11);
}

static boolean adminLogin() {
    System.out.println("=== Admin Login ===");
    System.out.print("Username: ");
    String username = scanner.nextLine();
    System.out.print("Password: ");
    String password = scanner.nextLine();
    return username.equals("admin") && password.equals("1234");
}

static void bookRoom() {
    System.out.print("Enter guest name: ");
    String name = scanner.nextLine();
    System.out.print("Enter room number: ");
    int room = scanner.nextInt();

    if (occupiedRooms.contains(room)) {
        System.out.println("Room already booked!");
        return;
    }

    System.out.print("Enter number of days: ");
    int days = scanner.nextInt();
    scanner.nextLine();

    System.out.print("Enter room type (AC/Non-AC): ");
    String type = scanner.nextLine();
    System.out.print("Enter payment method (Cash/Card): ");
    String paymentMethod = scanner.nextLine();
    double rate = type.equalsIgnoreCase("AC") ? acRatePerDay : nonAcRatePerDay;

    Guest guest = new Guest(name, room, days, type.toUpperCase(), rate, paymentMethod);
    guestList.add(guest);
    occupiedRooms.add(room);
    System.out.println("Room booked successfully! Total bill: $" + guest.bill);
}

static void viewGuests() {
    if (guestList.isEmpty()) {
        System.out.println("No guests found.");
        return;
    }
    for (Guest g : guestList) {
        System.out.println(g);
    }
}

static void searchGuest() {
    System.out.print("Enter guest name to search: ");
    String name = scanner.nextLine();
    boolean found = false;
    for (Guest g : guestList) {
        if (g.name.equalsIgnoreCase(name)) {
            System.out.println(g);
            found = true;
        }
    }
    if (!found) {
        System.out.println("Guest not found.");
    }
}

static void viewAvailability() {
    System.out.println("Available rooms (1-100):");
    for (int i = 1; i <= 100; i++) {
        if (!occupiedRooms.contains(i)) {
            System.out.print(i + " ");
        }
    }
    System.out.println();
}

static void checkoutGuest() {
    System.out.print("Enter room number to checkout: ");
    int room = scanner.nextInt();
    scanner.nextLine();

    Guest toRemove = null;
    for (Guest g : guestList) {
        if (g.roomNumber == room) {
            toRemove = g;
            break;
        }
    }

    if (toRemove != null) {
        guestList.remove(toRemove);
        occupiedRooms.remove(room);
        System.out.println("Guest checked out successfully.");
    } else {
        System.out.println("Room not found or already empty.");
    }
}

static void deleteGuestByName() {
    System.out.print("Enter guest name to delete: ");
    String name = scanner.nextLine();
    boolean removed = guestList.removeIf(g -> g.name.equalsIgnoreCase(name));
    if (removed) {
        System.out.println("Guest deleted successfully.");
    } else {
        System.out.println("Guest not found.");
    }
}

static void sortGuestsByBill() {
    guestList.sort((a, b) -> Double.compare(b.bill, a.bill));
    System.out.println("Guests sorted by bill (High to Low):");
    viewGuests();
}

static void filterByRoomType() {
    System.out.print("Enter room type to filter (AC/Non-AC): ");
    String type = scanner.nextLine().toUpperCase();
    for (Guest g : guestList) {
        if (g.roomType.equalsIgnoreCase(type)) {
            System.out.println(g);
        }
    }
}

static void viewReport() {
    int totalGuests = guestList.size();
    double totalRevenue = guestList.stream().mapToDouble(g -> g.bill).sum();
    int totalRoomsOccupied = occupiedRooms.size();

    System.out.println("\n===== Hotel Report =====");
    System.out.println("Total Guests: " + totalGuests);
    System.out.println("Total Revenue: $" + totalRevenue);
    System.out.println("Rooms Occupied: " + totalRoomsOccupied);
    System.out.println("Rooms Available: " + (100 - totalRoomsOccupied));
}

static void employeeManagement() {
    System.out.println("\n=== Employee Management ===");
    System.out.println("1. Add Employee");
    System.out.println("2. View Employees");
    System.out.println("3. Search Employee by ID");
    System.out.print("Enter choice: ");
    int choice = scanner.nextInt();
    scanner.nextLine();

    switch (choice) {
        case 1 -> {
            System.out.print("Enter employee name: ");
            String name = scanner.nextLine();
            System.out.print("Enter employee ID: ");
            String id = scanner.nextLine();
            System.out.print("Enter role: ");
            String role = scanner.nextLine();
            employeeList.add(new Employee(name, id, role));
            System.out.println("Employee added successfully.");
        }
        case 2 -> {
            if (employeeList.isEmpty()) {
                System.out.println("No employees found.");
            } else {
                for (Employee emp : employeeList) {
                    System.out.println(emp);
                }
            }
        }
        case 3 -> {
            System.out.print("Enter employee ID to search: ");
            String id = scanner.nextLine();
            boolean found = false;
            for (Employee emp : employeeList) {
                if (emp.id.equalsIgnoreCase(id)) {
                    System.out.println(emp);
                    found = true;
                    break;
                }
            }
            if (!found) {
                System.out.println("Employee not found.");
            }
        }
        default -> System.out.println("Invalid choice!");
    }
}

}
