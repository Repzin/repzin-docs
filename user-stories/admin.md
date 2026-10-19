# Admin (Gym Owner) User Stories

1. As a gym owner, I want to register members and track their transactions.
   - Use a step-by-step wizard requiring only a name, phone number, and emergency phone number.
   - Allow biometric enrollment to be initiated during registration.
   - Assign each member a unique, human-readable member ID.

2. As a gym owner, I want to add, update, remove, publish, and unpublish membership packages.
   - Support packages with different prices and validity periods.
   - Support couple packages that apply to each member of the group.

3. As a gym owner, I want to create employee accounts with limited access and track employee activity.
   - Assign an initial password and require a password change at first login.

4. As a gym owner, I want to assign fine-grained permissions to individual employees to restrict access and hide sensitive information.

5. As a gym owner, I want to record membership payments so members can unlock the door using biometrics.
   - Show current membership details when recording a payment.
   - Display the start date and calculate the end date automatically.
   - Send an automatic confirmation with the payment date, end date, amount paid, transaction ID, and membership ID.
   - Transaction IDs do not need to be human-readable.

6. As a gym owner, I want members to complete registration themselves to reduce my workload.
   - Provide a registration URL.
   - Require gym owner approval to complete registration.

7. As a gym owner, I want to view attendance and arrival times.
   - Default to today and allow filtering by date.
   - Show all attendance records, including multiple visits on the same day.

8. As a gym owner, I want to send automatic reminders before membership packages expire.
   - Include the end date and membership ID.

9. As a gym owner, I want to send an automatic notification when a membership expires.
   - Include the same basic membership information as the expiry reminder.

10. As a gym owner, I want to send automatic messages to absent members to encourage attendance.
    - **To discuss:** Define when these messages should be sent.

11. As a gym owner, I want to view money collected in a selected month to support business decisions.

12. As a gym owner, I want to identify members who attend without paying their membership fees.
