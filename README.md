[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/vOYIK_Kq)

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class TicketBookingSystem {
    private final boolean[] seats;
    private final Lock lock = new ReentrantLock();

    public TicketBookingSystem(int numberOfSeats) {
        seats = new boolean[numberOfSeats]; // false means unbooked
    }

    public boolean bookSeat(int seatNumber, String customer) {
        lock.lock();
        try {
            if (seatNumber < 0 || seatNumber >= seats.length) {
                System.out.println(customer + " tried to book an invalid seat number: " + seatNumber);
                return false;
            }
            if (!seats[seatNumber]) {
                seats[seatNumber] = true;
                System.out.println(customer + " successfully booked seat " + seatNumber);
                return true;
            } else {
                System.out.println(customer + " tried to book an already booked seat " + seatNumber);
                return false;
            }
        } finally {
            lock.unlock();
        }
    }
}

class BookingThread extends Thread {
    private final TicketBookingSystem system;
    private final int seatNumber;
    private final String customer;

    public BookingThread(TicketBookingSystem system, int seatNumber, String customer, int priority) {
        this.system = system;
        this.seatNumber = seatNumber;
        this.customer = customer;
        setPriority(priority);
    }

    @Override
    public void run() {
        system.bookSeat(seatNumber, customer);
    }
}

public class TicketBookingApp {
    public static void main(String[] args) {
        TicketBookingSystem system = new TicketBookingSystem(10); // 10 seats available

        // Simulating multiple customers trying to book seats
        Thread vip1 = new BookingThread(system, 3, "VIP_1", Thread.MAX_PRIORITY);
        Thread vip2 = new BookingThread(system, 3, "VIP_2", Thread.MAX_PRIORITY);
        Thread normal1 = new BookingThread(system, 3, "Normal_1", Thread.NORM_PRIORITY);
        Thread normal2 = new BookingThread(system, 5, "Normal_2", Thread.NORM_PRIORITY);

        vip1.start();
        vip2.start();
        normal1.start();
        normal2.start();
    }
}
