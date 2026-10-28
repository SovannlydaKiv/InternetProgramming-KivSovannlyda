# QNA

1. You refresh the browser on /events/2 and the page loads. Which program answered that request in development, and what must the production server do so it still works after deployment? 

> In development, the Vite dev server intercepts the request and serves index.html. In production, the web server (like Nginx or Apache) must be configured with a fallback rule to serve index.html for all unknown paths so Vue Router can take over client-side routing.

2. The guard stops students from opening /events/new. Can a student still create an event? How, and what must stop them? 

> Yes, they can bypass the frontend UI entirely and send a POST request directly to the API using tools like curl or Postman. To actually prevent this, the backend server must enforce role-based authorization checks on the endpoint and reject unauthorized requests.

3. EventDetailPage loads its event in a watch on the id with immediate: true, not in onMounted. What bug would onMounted alone cause? 

> If a user navigates directly from one event to another like from /events/1 to /events/2, Vue reuses the existing component instance instead of destroying and remounting it. Because it is reused, onMounted does not trigger again, and the page would mistakenly continue showing the first event's data.

4. Why does EventsPage use storeToRefs(store) instead of const { events } = store?

> Using standard destructuring like const { events } = store breaks reactivity, meaning the component will not update when the store's data changes. storeToRefs extracts properties from the store while wrapping them in refs, ensuring the reactive connection is preserved.

5. Booking makes two requests: POST the booking, then PATCH the seat count. What happens to the data if the second one fails? How could the server avoid this? 

> If the PATCH request fails, the system enters an inconsistent state where the booking is saved but the event's remaining capacity is not reduced. The server can avoid this by combining both actions into a single database transaction, ensuring that if the seat update fails, the booking creation is rolled back.