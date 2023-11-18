# User Management Dashboard

The User Management Dashboard is an application designed to facilitate efficient user management, enabling user details viewing and account creation. It offers robust functionalities to manage and analyze user data effectively.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Technologies Used](#technologies-used)
- [Installation](#installation)

---

## Overview

The User Management Dashboard provides an intuitive interface for managing user information, generating reports, and creating new user accounts.

---

## Features

### 1. View User Details

- Access comprehensive user information including username, email, phone, ID, and creation date.

### 2. Create New User Accounts

- Easily create new user accounts with required information for onboarding.

### 3. Generate Reports for Users

- Generate detailed reports for individual users for better data analysis and insights.

### 4. Search and Filter User Data

- Quickly search and filter user data based on different parameters for streamlined access.

---

## Technologies Used

The User Management Dashboard leverages the following technologies:

- **Vue.js**: Frontend development framework for building reactive and efficient user interfaces.
- **Nuxt.js**: A Vue.js framework that enhances the application with server-side rendering, routing, and other features.
- **JavaScript (ES6+)**: Utilized for better code readability and functionality.
- **HTML5 & CSS3**: Employed for a visually appealing and responsive design.

---

## Installation

### Prerequisites

Ensure you have the following installed:

- [Node.js](https://nodejs.org/en/)
- [NPM](https://www.npmjs.com/)

### Installation Steps

```bash
# Clone the repository
$ git clone https://github.com/kamal63783/user-management-dashboard.git

# Navigate to the project directory
$ cd user-management-dashboard

# Install dependencies
$ npm install

# Build the application for production
$ npm run build

# Launch the production server
$ npm run start

# Generate a static project
$ npm run generate

# To run the application in development mode
$ npm run dev
```

## For detailed explanation on how things work, check out the [documentation](https://nuxtjs.org).

**Note**: In User Details tab, generating a report for user details is only feasible after conducting a search. And when using the account creation functionality, entering the email "failed@example.com" intentionally triggers the failed message for testing purposes.
