Smart Parking Lot 

The Smart Parking Lot System manages vehicle entry and exit, allocates parking spots based on vehicle size and availability, and calculates parking fees. The system comprises several components that interact to provide these functionalities.

Document Information
Status	    Owner	        Authors	     Version   Reviewed By	  Approved By
  1	    Piyush Kumar	  Piyush Kumar	   1	


  Components

    1. Entry/Exit Gate System
    2. Parking Spot Management
    3. Fee Calculation Module
    4. Database
    5. Concurrency Handling    


1. Entry/Exit Gate System
  This component manages vehicle entry and exit, including recording entry/exit times and triggering parking spot allocation.

  Components

    a. Gate Controller: Handles signals to open and close gates.
    b. Scanner/Camera: Captures vehicle details (license plate, size).
    c. Entry/Exit Processor: Processes the scanned data and communicates with the central system.

  Workflow

   Vehicle Entry:

     -> Scanner captures vehicle details.
     -> Entry Processor sends vehicle details to the central system.
     -> Central system allocates a parking spot.
     -> Gate Controller receives a signal to open the gate.
     -> Entry Processor updates the database with entry time and allocated spot.

   Vehicle Exit:

     -> Scanner captures vehicle details.
     -> Exit Processor sends vehicle details to the central system.
     -> Central system calculates the parking fee.
     -> Payment processed externally.
     -> Gate Controller receives a signal to open the gate.
     -> Exit Processor updates the database with exit time and fee.

2. Parking Spot Management System
This module handles the allocation and real-time availability of parking spots.

  Components

    a. Spot Allocator: Algorithm to find available spots based on vehicle size.
    b. Spot Database: Stores information about each parking spot.
    c. Availability Tracker: Real-time updates on spot availability.

  Workflow

    -> Spot Allocator queries the Spot Database for available spots.
    -> Allocates the nearest available spot to the vehicle.
    -> Updates the Availability Tracker and Spot Database.

  Parking Spot Allocation Algorithm

    a. Identify Vehicle Type: Determine if the vehicle is a motorcycle, car, or bus.
    b. Search Available Spots: Find the nearest available spot for the vehicle type.
    c. Allocate Spot: Reserve the spot for the vehicle.
    d. Update Database: Mark the spot as occupied in the database.

3. Fee Calculation Module
This module calculates the parking fee based on the vehicle type and duration of stay.

Components

a. Fee Calculator: Logic to compute fees.
b. Rate Database: Stores rates for different vehicle types and durations.
c. Transaction Database: Records each parking transaction.

Workflow

-> Calculate the duration of stay.
-> Retrieve rate from the Rate Database.
-> Compute the fee based on duration and vehicle type.
-> Update the Transaction Database with the calculated fee.

4. Database Schema
Design a schema to manage parking spots, vehicles, and transactions.

Tables

4.1 ParkingSpots 

spot_id: INT, PRIMARY KEY
spot_size: ENUM('motorcycle', 'car', 'bus')
is_occupied: BOOLEAN
floor: INT

4.2 Vehicles

vehicle_id: VARCHAR, PRIMARY KEY
vehicle_size: ENUM('motorcycle', 'car', 'bus')
entry_time: TIMESTAMP
exit_time: TIMESTAMP

4.3 ParkingTransactions

transaction_id: INT, PRIMARY KEY
vehicle_id: VARCHAR, FOREIGN KEY REFERENCES Vehicles(vehicle_id)
spot_id: INT, FOREIGN KEY REFERENCES ParkingSpots(spot_id)
entry_time: TIMESTAMP
exit_time: TIMESTAMP
fee: DECIMAL

4.4 Rates

vehicle_size: ENUM('motorcycle', 'car', 'bus'), PRIMARY KEY
rate_per_hour: DECIMAL

5. Concurrency Handling
Ensure the system can handle multiple vehicles entering or exiting simultaneously.
 
  Techniques
    5.1 Database Locking: Use row-level locking to manage concurrent updates to the same parking spot.
    5.2 Atomic Transactions: Ensure each vehicle entry or exit operation is atomic to prevent data inconsistencies.
    5.3 Optimistic Concurrency Control: Handle conflicts by retrying the operation if a conflict is detected.


Detailed Interactions

  1. Vehicle Entry Interaction

    -> Vehicle arrives at the entry gate.
    -> Scanner captures vehicle details (license plate, size).
    -> Entry Processor sends details to the central system.
    -> Central system invokes Spot Allocator to find an available spot.
    -> Spot Allocator updates Spot Database and Availability Tracker.
    -> Gate Controller opens the gate for vehicle entry.
    -> Entry Processor records the entry time and allocated spot in Vehicles and ParkingTransactions tables.

  2. Vehicle Exit Interaction

    -> Vehicle arrives at the exit gate.
    -> Scanner captures vehicle details (license plate, size).
    -> Exit Processor sends details to the central system.
    -> Central system calculates the parking fee using Fee Calculator.
    -> Fee Calculator computes the fee based on duration and vehicle type.
    -> Exit Processor records the exit time and fee in Vehicles and ParkingTransactions tables.
    -> Gate Controller opens the gate for vehicle exit after payment is processed.

Sequence Diagrams

  Vehicle Entry Sequence Diagram

    Vehicle -> EntryGate: Arrives
    EntryGate -> Scanner: Capture Details
    Scanner -> EntryProcessor: Send Details
    EntryProcessor -> CentralSystem: Process Entry
    CentralSystem -> SpotAllocator: Allocate Spot
    SpotAllocator -> SpotDatabase: Update Spot Status
    SpotAllocator -> AvailabilityTracker: Update Availability
    SpotAllocator -> CentralSystem: Spot Allocated
    CentralSystem -> EntryProcessor: Spot Allocated
    EntryProcessor -> GateController: Open Gate
    GateController -> EntryGate: Open
    EntryProcessor -> VehiclesTable: Update Entry
    EntryProcessor -> ParkingTransactionsTable: Record Transaction

  Vehicle Exit Sequence Diagram

    Vehicle -> ExitGate: Arrives
    ExitGate -> Scanner: Capture Details
    Scanner -> ExitProcessor: Send Details
    ExitProcessor -> CentralSystem: Process Exit
    CentralSystem -> FeeCalculator: Calculate Fee
    FeeCalculator -> RatesDatabase: Retrieve Rates
    FeeCalculator -> FeeCalculator: Compute Fee
    FeeCalculator -> CentralSystem: Fee Computed
    CentralSystem -> ExitProcessor: Fee Computed
    ExitProcessor -> PaymentSystem: Process Payment
    PaymentSystem -> ExitProcessor: Payment Confirmed
    ExitProcessor -> GateController: Open Gate
    GateController -> ExitGate: Open
    ExitProcessor -> VehiclesTable: Update Exit
    ExitProcessor -> ParkingTransactionsTable: Record Transaction

Conclusion
The low-level system design for the Smart Parking Lot System includes detailed components and workflows for managing vehicle entry/exit, parking spot allocation, fee calculation, and concurrency handling. This design ensures efficient, real-time parking management suitable for an urban environment.

