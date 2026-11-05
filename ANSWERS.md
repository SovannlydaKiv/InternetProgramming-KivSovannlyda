# Answers for question

1. DELETE /events/2 returns 204 and the second call returns 404. Is DELETE still idempotent? Explain with what idempotent means.
    > Yes, `DELETE` is still idempotent. Idempotent means that making the same request multiple times leaves the server's data in the exact same state as making it once. Even though the HTTP status code changes from 204 to 404 on the second try, the end result—the event being gone—remains exactly the same.


2. The Vue app on port 5173 calls the API on port 3000. Which program blocks the response when CORS is not configured, and why can curl read it anyway?
    > The user's web browser is the program that blocks the response to protect against unauthorized cross-site requests. `curl` can read it anyway because it is just a basic command-line tool, not a browser, so it does not care about or enforce CORS security policies.


3. EventsController never writes new EventsService(). Who creates the service, when, and how many are created?
    > The NestJS framework itself creates the service when the application first starts up. It uses a pattern called dependency injection to create exactly one instance of the service (a singleton) and automatically passes it into the controller.


4. A client sends { "title": "Pitch Night", ..., "booked": 50 }. Which line of code refuses it, and what would happen to booked without it?
    > The `forbidNonWhitelisted: true` setting inside the `ValidationPipe` configuration (in `src/setup.ts`) refuses the request with a 400 error. Without it, the server would accept the extra data, and a malicious client could potentially overwrite the `booked` count to cheat the seating limit.


5. Why is the “not enough seats” rule in BookingsService and not in BookingsController or only in the Vue form?
    > The service layer is specifically designed to hold your core business logic and rules, while controllers should only map HTTP requests to those services. We can't rely only on the Vue form because a user could easily bypass the frontend entirely (using tools like `curl` or Postman) and force overbookings directly into the database.