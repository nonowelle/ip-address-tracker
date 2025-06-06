# Frontend Mentor - IP Address Tracker Solution

This is a solution to the [IP address tracker challenge on Frontend Mentor](https://www.frontendmentor.io/challenges/ip-address-tracker-I8-0yYAH0). Frontend Mentor challenges help you improve your coding skills by building realistic projects.

## Table of Contents

- [Overview](#overview)
  - [The Challenge](#the-challenge)
  - [Screenshot](#screenshot)
  - [Links](#links)
- [My Process](#my-process)
  - [Built With](#built-with)
  - [What I Learned](#what-i-learned)
  - [Continued Development](#continued-development)
  - [Useful Resources](#useful-resources)
- [Author](#author)

## Overview

### The Challenge

Users should be able to:

- View the optimal layout for each page depending on their device's screen size
- See hover states for all interactive elements on the page
- See their own IP address on the map on the initial page load
- Search for any IP addresses or domains and see the key information and location

### Links

- Solution URL: [GitHub Repository](https://github.com/nonowelle/ip-address-tracker)
- Live Site URL: [Live Demo](https://ip-address-tracker-master-oa6rtgjs3-nonowelles-projects.vercel.app/)

### Built With

- **Vue 3** - Progressive JavaScript framework
- **Vue Composition API** - For reactive state management and component logic
- **Leaflet.js** - Open-source library for interactive maps
- **IPify Geolocation API** - For IP address geolocation data
- **SCSS** - CSS preprocessor for enhanced styling

### What I Learned

Working on this project helped me strengthen my understanding of several key concepts:

**Vue Composition API**: I became more comfortable with the reactive system and how to structure component logic using the Composition API pattern.

**Integrating Third-party APIs**: I learned how to work with the ipify API.

**Interactive Maps with Leaflet**: Implementing dynamic map functionality, including updating map markers and centering the view based on geolocation data.

### Continued Development

Areas I want to continue focusing on in future projects:

- **Error Handling**: Implementing more robust error handling for API failures and edge cases
- **Testing**: Adding unit tests for component logic and API interactions
- **Accessibility**: Improving keyboard navigation and screen reader support for map interactions

### Useful Resources

- [Vue 3 Composition API Documentation](https://vuejs.org/guide/composition-api-introduction.html) - Essential for understanding the reactive system and composition patterns
- [Leaflet.js Documentation](https://leafletjs.com/reference.html) - Comprehensive guide for map functionality and customization
- [IPify API Documentation](https://geo.ipify.org/docs) - Clear documentation for the geolocation API endpoints

## Author

- Website - [Marie-](menoel.com)
- GitHub - [@yourusername](https://github.com/nonowelle)
- LinkedIn - [Your Name](https://www.linkedin.com/in/marie-%C3%A8ve-no%C3%ABl/)

---

## Setup Instructions

1. Clone the repository

```bash
git clone https://github.com/nonowelle/ip-address-tracker.git
```

2. Install dependencies

```bash
npm install
```

3. Get your free API key from [IPify](https://geo.ipify.org/) and add it to your environment variables

```bash
# Create .env file
VITE_IPIFY_API_KEY=your_api_key_here
```

4. Run the development server

```bash
npm run dev
```

5. Build for production

```bash
npm run build
```

## Features

- 🔍 **IP/Domain Search** - Search for any IP address or domain name
- 🗺️ **Interactive Map** - View location on an interactive Leaflet map
- 📱 **Responsive Design** - Optimized for all device sizes
- ⚡ **Real-time Updates** - Instant location updates on search
- 🎯 **User Location** - Automatically detects and displays user's IP on load

## API Usage

This project uses the IPify Geolocation API to fetch IP address information. The API provides:

- IP Address
- Location (City, Region, Country)
- Timezone
- ISP Information
- Latitude/Longitude coordinates

_Note: You'll need to sign up for a free account at IPify to get your API key._
