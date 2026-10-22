## Answers to the questions: 

1. In EventList, what goes wrong if you use the array index as :key and the list gets filtered while a card has local state (for example an open details panel)? 

> If you filter the list, the items shift but the array indices stay the same (0, 1, 2). Vue reuses the DOM elements based on the index key, so the local state (like an open details panel) will accidentally attach to whichever new event takes that index position, mixing up the UI data.

2. Why is visible a computed and not a function called from the template? What is the difference when you type in the search box? 

> A computed property caches its result and only recalculates when its specific dependencies (like the search text or category) change. If it were a function called from the template, Vue would recalculate the filter on every single render cycle, which is much less efficient as you type.

3. EventCard emits book instead of changing event.booked. Give two reasons. 

> First, props in Vue are read-only, so mutating event.booked directly inside the child component triggers a warning. Second, the parent component (App.vue) owns the global state, so emitting the action allows the parent to coordinate all state changes (like the header count) consistently across the app.

4. You write const { booked } = reactive(event); booked++. The page does not change. Why? 

> Destructuring a primitive value (like a number) out of a reactive object breaks its reactivity. Writing booked++ only increments a disconnected local variable, leaving the actual reactive event object and the page UI unchanged.

5. Where does myBookings live, and why not in EventCard or EventList? 

> App.vue because it represents global application state. It needs to be accessed by the top-level header to display the total booking count, which would be impossible if the data were isolated inside the EventCard or EventList child components.