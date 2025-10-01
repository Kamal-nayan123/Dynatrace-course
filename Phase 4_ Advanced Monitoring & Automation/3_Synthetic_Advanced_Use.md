# Phase 4: Advanced Monitoring & Automation - Synthetic Advanced Use

## Introduction
While basic synthetic monitoring involves simple uptime checks, advanced use cases allow you to proactively monitor complex user journeys, internal applications, and multi-step API workflows. These advanced capabilities are crucial for ensuring the reliability and performance of business-critical processes and for testing applications that are not exposed to the public internet.

## 1. Browser vs. API Tests

Synthetic monitoring in Dynatrace comes in two main flavors: Browser tests and API tests (also known as HTTP monitors).

### Browser Tests (Clickpaths)
*   **Technical Definition:** A browser test, or clickpath, uses a real web browser (like Chrome) running on a synthetic location to execute a recorded sequence of user interactions. It simulates a complete user journey, loading all page elements including HTML, CSS, JavaScript, and images. It measures user-centric metrics like Visually Complete, Speed Index, and Largest Contentful Paint (LCP).
*   **Simple Definition:** This is like having a robot use a real web browser to click through your website, just like a user would. It tests the full, end-to-end user experience, from the frontend to the backend.
*   **When to use it:**
    *   To monitor critical user journeys (e.g., login, search, checkout, registration).
    *   To measure the true, user-perceived performance of your web application.
    *   To validate that complex frontend applications (like those built with React, Angular, or Vue.js) are rendering and functioning correctly.
*   **Practical Example:** An airline wants to ensure its booking process is always working. They record a browser clickpath that:
    1.  Navigates to the homepage.
    2.  Fills out the "Book a Flight" form.
    3.  Selects a flight from the results.
    4.  Proceeds to the passenger details page.
    This test runs every 10 minutes from multiple geographic locations. If any step fails or takes too long, the team is immediately alerted.

### API Tests (HTTP Monitors)
*   **Technical Definition:** An API test, or HTTP monitor, makes one or more HTTP requests to specific API endpoints to check their availability, correctness, and performance. It does not use a browser and does not render any UI. It focuses on validating the API contract, such as checking the HTTP status code, response headers, and the content of the JSON/XML payload.
*   **Simple Definition:** This is like having a robot directly call the "back door" of your application (the API) without ever opening the "front door" (the website). It's a fast and efficient way to check if your backend logic and data sources are working correctly.
*   **When to use it:**
    *   To monitor the health of internal or public microservices and APIs.
    *   To validate multi-step API workflows (e.g., get an auth token, then use the token to fetch data).
    *   To monitor APIs used by mobile apps or third-party integrators.
    *   For lightweight, high-frequency uptime checks.
*   **Practical Example:** A mobile banking app relies on a `balance-check` API. The backend team creates a multi-step API test that:
    1.  Sends a POST request to `/oauth/token` to get an authentication token.
    2.  Extracts the token from the response body.
    3.  Sends a GET request to `/api/v2/balance`, using the extracted token in the `Authorization` header.
    4.  Asserts that the HTTP response code is `200 OK` and that the response body contains the key `"currency": "USD"`.
    This test runs every minute to ensure the core functionality of the mobile app is always available.

## 2. Private Synthetic Locations

*   **Technical Definition:** A private synthetic location is a Dynatrace ActiveGate configured to run synthetic monitors from within your own private network (e.g., your data center or private cloud VPC). This allows you to run browser and API tests against internal applications that are not accessible from the public internet.
*   **Simple Definition:** Sometimes you need to test websites that are only for your employees, like an internal HR portal. Private synthetic locations let you set up your own "robot testing station" inside your company's private network to monitor these internal-only applications.
*   **When to use it:**
    *   To monitor internal applications (e.g., SharePoint, Confluence, internal HR systems).
    *   To test applications in a pre-production or staging environment before they are exposed to the public.
    *   To measure performance from a specific network location, like an office branch, to understand the experience of employees at that location.
*   **Practical Example:** A company has a critical internal CRM system hosted in its own data center. The IT team installs an ActiveGate on a server in that data center and enables the "Synthetic" capability. They then create a browser clickpath to test the CRM's login and customer search functionality. In the test configuration, they select their newly created "Data Center - Frankfurt" private location instead of a public one. This ensures they can proactively monitor the availability and performance of this critical internal tool for their employees.

## 3. Scripted Tests

*   **Technical Definition:** While many tests can be created with the visual recorder, Dynatrace also supports scripted tests for more complex scenarios. Both browser and API tests can be converted to a script format (JavaScript for browser tests, JSON for API tests). This allows for advanced logic, such as using variables, handling dynamic data, writing custom validation code, and controlling test flow with conditional statements.
*   **Simple Definition:** Scripted tests are for when you need your robot to do something really smart that you can't just record by clicking. It's like writing a detailed program for the robot, allowing it to handle complex situations, use secret codes (like API keys), and make decisions during the test.
*   **When to use it:**
    *   **Dynamic Data:** When a test needs to use data that changes, like the current date or a unique ID.
    *   **Authentication:** Handling complex authentication flows like OAuth or using credentials securely stored as variables.
    *   **Conditional Logic:** Changing the test's behavior based on the result of a previous step (e.g., "if the search returns 0 results, then stop the test").
    *   **Custom Validation:** Writing JavaScript code to perform complex validation on a response payload that goes beyond simple text matching.
*   **Practical Example:** A developer needs to test a "create user" API endpoint. The username must be unique for every test run. They create a scripted API test. In the pre-execution script, they use JavaScript to generate a random username and store it as a variable: `api.variables.set("uniqueUsername", "testuser_" + Date.now());`. In the HTTP request body, they use this variable: `{ "username": "{{uniqueUsername}}", "email": "test@example.com" }`. This ensures that every test run attempts to create a new, unique user, accurately simulating the API's real-world usage and avoiding errors from duplicate entries.