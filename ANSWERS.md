# Answers

**Name: Kiv Sovannlyda**

**ID: p20240001**

1.	Why does Booking have an eventId column but Event has no bookingIds column? Where is the “many” side of a 1-N relation stored?
    > The reason why is that in a relational database, if we store a list of IDs inside a single `Event` cell breaks normalization and makes it impossible to enforce clean foreign keys. Instead, the `many` side of a one-to-many relationship is always stored on the child table, so each `Booking` row holds a single `eventId` foreign key pointing back to its parent

2.	booked repeats information we could compute with COUNT and SUM over bookings. Why keep it, and what must the code do so it never becomes wrong?
    > Keeping a `booked` counter directly on `Event` makes reading available seats much faster because we don't have to look through and total up the `Booking` table every time someone views an event, and it lets us check seat limits in a single `UPDATE` query. In order to keep it from ever drifting out of sync, every operation that creates or deletes a booking must increment or decrement `booked` inside the exact same database transaction

3.	What is the difference between prisma migrate dev and prisma generate? Which one changes the database?
    > The difference between prisma migrate dev and prisma generate is that: 
    > - prisma migrate dev compares your prisma schema against the database then generates the SQL migration files, and executes them to update the actual database tables
    > - prisma generate only reads your schema locally to rebuild the TypeScript client code inside `src/generated` so your editor and NestJS app know the updated types

4.	A page lists 20 events with their bookings. One version runs 1 query, another runs 21. Name the problem and show how include fixes it.
    > The problem is the N+1 query problem, where the app runs 1 query to fetch the 20 events and then runs 20 separate queries inside a loop to fetch the bookings for each event. In order to fix it we should pass `include: { bookings: true }` inside `prisma.event.findMany({ include: { bookings: true } })`, which tells prisma to fetch all events and their related bookings together upfront instead of querying them one by one

5.	Two students book the last seat at the same moment. Explain step by step why the read-check-update version can sell two seats and why the conditional updateMany cannot.
    > The read-check-update can sell 2 seats because in that version, both requests read `booked = 29` (out of 30) at the exact same millisecond, so both pass the JavaScript `29 + 1 <= 30` check, and both save `booked = 30` and create a booking that oversell the event by one seat. The conditional updateMany cannot because PostgreSQL locks the row during the update so the first request increments `booked` to `30` (`count = 1`), and when the second request runs immediately after, the `where` condition evaluates to false against the updated row, returning `count = 0` so our code safely throws a `409 Conflict`
    