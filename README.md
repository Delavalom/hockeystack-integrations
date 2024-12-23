# API Sample Test

## Getting Started

This project requires a newer version of Node. Don't forget to install the NPM packages afterwards.

You should change the name of the ```.env.example``` file to ```.env```.

Run ```node app.js``` to get things started. Hopefully the project should start without any errors.

## Explanations

The actual task will be explained separately.

This is a very simple project that pulls data from HubSpot's CRM API. It pulls and processes company and contact data from HubSpot but does not insert it into the database.

In HubSpot, contacts can be part of companies. HubSpot calls this relationship an association. That is, a contact has an association with a company. We make a separate call when processing contacts to fetch this association data.

The Domain model is a record signifying a HockeyStack customer. You shouldn't worry about the actual implementation of it. The only important property is the ```hubspot```object in ```integrations```. This is how we know which HubSpot instance to connect to.

The implementation of the server and the ```server.js``` is not important for this project.

Every data source in this project was created for test purposes. If any request takes more than 5 seconds to execute, there is something wrong with the implementation.

## Hubspot Meeting Task

There is an engagement type called meeting in HubSpot. When a salesperson meets with a contact, they create a meeting through HubSpot for it.

1. Just like how we've done it for contacts and companies, write a method in the `worker.js` file that pulls and processes the meetings. Pull the meeting title and some timestamp properties from the meeting object. This worker will ideally run on a daily basis to get newly modified meetings.

2. You need to insert two actions as a result of this processing: `Meeting Created` (when that meeting record was created in HubSpot) and `Meeting Updated` (whenever it isn’t a Created action).

3. Store which contact attended the meeting along with the meeting properties. HubSpot normally doesn't give this data at the same time with the meetings so you should find another way to get it. At the end, you should have each meeting as a separate action along with the **email** of the contact which attended the meeting.

4. Write a short debrief (about 8-10 sentences is enough) on how you would improve this project in terms of (1) code quality and readability, (2) project architecture, and especially (3) code performance.

5. Send the code and the debrief to us through a new Github repo that you create.

Disclaimer: You'll primarily (but not only) work in the `worker.js` file. The code itself isn't very well written, but this is part of the test. The tradeoffs you make to deliver this feature tell us more about how you work in a fast-paced environment. Aim for around 2-3 hours of work.


## How I would improve this project:

### 1 - In terms of code quality and readability:

We break down the logic into smaller, more focused files to improve clarity and separation of concerns. I would improve `Error Handling` introducing a more structured error-handling approach for robustness. For common logic such as API requests and token refreshing is centralized to avoid repetition. Unified the data processing for contacts, companies, and meetings to a reusable function.

This is how the architecture would look like:

```txt
src/
  ├── config/
  │   └── constants.js  # Constants like API credentials and configuration
  ├── hubspot/
  │   ├── api.js       # HubSpot API client setup and helper functions
  │   ├── token.js     # Token management and refresh logic
  │   └── pullData.js  # Main logic for pulling data from HubSpot
  ├── queue/
  │   └── index.js     # Queue management logic
  ├── utils/
  │   └── helpers.js   # Helper functions like filtering null values
  ├── models/
  │   └── Domain.js    # Database models
  └── app.js         # Main entry point
```

### 2 - Project architecture:

I would implement this project architecture:

![diagram](/assets/image.png)

By utilizing two separate queues, I ensure that one queue is dedicated to processing tasks while the other handles retries. While queues can inherently include retry logic, separating these responsibilities allows the retry mechanism to be decoupled from the processing code. This separation simplifies debugging, making it easier to trace and resolve issues.

This improvement aligns with our goal of enhancing debugging tools to enable faster iteration, especially in a startup environment where quick issue identification and resolution are critical. By putting the right mechanisms in place, we can maintain agility and ensure smoother operations as we scale.

### 3 - Especially code performance:

The architecture I have implemented for the queue is designed to optimize processing for calls that successfully complete without failures. When working to improve performance with third-party vendors, our options are often limited. However, we can enhance efficiency by leveraging a worker queue, enabling parallel processing, and aggregating domain data before saving—similar to a fan-out, fan-in pattern. Additionally, using a separate server written in Golang, rather than JavaScript, can further improve performance with easy use of multi-threads.
Here’s an improved version of your text:

## Final Thoughts

Instead of spending time implementing changes directly in the code, I chose to outline the improvement plans in this README. Given my limited time and context to fully explore how HubSpot behaves and how to integrate meetings processing, I hope this approach is clear and understandable. Thank you for taking the time to review it.
