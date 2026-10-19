<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->
<a id="readme-top"></a>

<!-- PROJECT SHIELDS -->
[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]

<!-- PROJECT TITLE -->
<br />
<div align="center">
  <h3 align="center">Air Traffic Manager</h3>

  <p align="center">
    A concurrent air-traffic simulation for one airport: aircraft approach, burn fuel, declare
    emergencies, and compete for four runways while a MongoDB-backed control room tracks every
    change.
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/AirTrafficManager"><strong>Explore the repository »</strong></a>
    <br />
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/AirTrafficManager/issues">Report Bug</a>
    &middot;
    <a href="https://github.com/Elouahabi-Naoufal/AirTrafficManager/issues">Request Feature</a>
  </p>
</div>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>

<!-- ABOUT THE PROJECT -->
## About The Project

Air Traffic Manager is a desktop simulation of airport operations written in Java. Aircraft are
generated with realistic callsigns, types, fuel loads and distances, then updated in the background
while separate workers monitor fuel, manage runways, react to emergencies and produce weather
events. The GUI is a database-driven control room: it polls MongoDB and renders active aircraft,
runway state, a system log and live statistics.

The concurrency model is built from plain threads and a `ThreadPoolExecutor`. At startup
`AirTrafficSystem` creates eight worker threads:

* **1 aircraft-update worker** — advances every aircraft's position and fuel state.
* **1 fuel-monitoring worker** — escalates low and critical fuel situations.
* **4 runway-manager workers** — arbitrate the four runways (`RWY-09L`, `RWY-09R`, `RWY-27L`,
  `RWY-27R`).
* **1 emergency-handler worker** — processes declared emergencies.
* **1 weather worker** — injects weather events that affect in-flight aircraft.

A few implementation details worth knowing before reading the code:

* The simulation is **database-centric**. Workers read and write the `airTrafficControl` database;
  the Swing UI does not hold the authoritative state.
* Despite the JavaFX dependencies in `pom.xml`, the current window class
  (`com.atc.gui.AirTrafficControlGUI`) is a **Swing** `JFrame`. JavaFX is not used by the running UI.
* The Maven build targets **Java 25** (`maven.compiler.source`/`target`). Build with a JDK 25
  toolchain.
* MongoDB is opened at `mongodb://localhost:27017`; if it is unavailable the code logs the failure
  and the database-backed views stay empty rather than crashing the process.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Built With

* [![Java][java-shield]][java-url]
* [![Maven][maven-shield]][maven-url]
* [![MongoDB][mongodb-shield]][mongodb-url]
* [![JavaFX][javafx-shield]][javafx-url] — declared dependencies; the active GUI is Swing

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- GETTING STARTED -->
## Getting Started

### Prerequisites

* JDK 25
  ```sh
  java -version
  ```
* Maven 3.9+
  ```sh
  mvn -version
  ```
* A MongoDB server on `localhost:27017` (MongoDB 4.4+ is sufficient)
  ```sh
  mongod --version
  ```

### Installation

1. Clone the repository
   ```sh
   git clone https://github.com/Elouahabi-Naoufal/AirTrafficManager.git
   cd AirTrafficManager
   ```
2. Start MongoDB locally.
3. Optionally create the collections and indexes used by the project
   ```sh
   mongosh setup_mongodb.js
   ```
   The application also creates its runtime collections on demand.
4. Compile
   ```sh
   mvn clean compile
   ```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- USAGE -->
## Usage

Launch the application through the configured JavaFX Maven plugin entry point:

```sh
mvn javafx:run
```

The control room opens with four runways, three generated flights and the eight background workers
already running. From the window you can:

* **Generate Aircraft** — insert a new flight with a random airline, aircraft type, fuel load,
  origin, destination and priority.
* **Add Runway** — register another runway in the database.
* **Fire / Medical / Security / Fuel Critical / Weather Storm / Fuel Low** — trigger an emergency
  scenario with a matching priority.
* **Clear Log** — clear the on-screen event log.

The MongoDB database is `airTrafficControl`, with collections including `active_aircraft`,
`runways`, `airports`, `aircraft_types`, `runway_events`, `emergency_events` and `weather_events`.
The schema and project specification are in [`Docs/`](https://github.com/Elouahabi-Naoufal/AirTrafficManager/tree/main/Docs).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ROADMAP -->
## Roadmap

- [x] Four-runway airport with database-backed runway state
- [x] Background workers for aircraft updates, fuel, runways, emergencies and weather
- [x] Swing control room with live tables and statistics
- [x] MongoDB persistence for runtime events
- [ ] Replace the Swing window with a real JavaFX UI, or remove the unused JavaFX dependencies
- [ ] Add an automated test suite (`src/test/java` currently contains only a `.gitkeep`)
- [ ] Extract the hard-coded simulation values in `SimulationConfig` into a configuration file
- [ ] Add graceful handling for a MongoDB outage in every worker, not only at startup

See the [open issues](https://github.com/Elouahabi-Naoufal/AirTrafficManager/issues) for a full list of proposed features and known issues.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Top contributors:

<a href="https://github.com/Elouahabi-Naoufal/AirTrafficManager/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=Elouahabi-Naoufal/AirTrafficManager" alt="contrib.rocks image" />
</a>

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- LICENSE -->
## License

No `LICENSE` file is bundled with this repository. This project is not licensed — all rights reserved.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTACT -->
## Contact

Naoufal Elouahabi
* LinkedIn: [https://www.linkedin.com/in/elouahabi-naoufal/](https://www.linkedin.com/in/elouahabi-naoufal/)
* Email: [elouahabi-naoufal1@gmail.com](mailto:elouahabi-naoufal1@gmail.com)

Project Link: [https://github.com/Elouahabi-Naoufal/AirTrafficManager](https://github.com/Elouahabi-Naoufal/AirTrafficManager)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

* [MongoDB Java Driver](https://www.mongodb.com/docs/drivers/java/sync/current/)
* [OpenJFX](https://openjfx.io/)
* [Apache Maven](https://maven.apache.org/)
* [Best-README-Template](https://github.com/othneildrew/Best-README-Template)
* [Img Shields](https://shields.io)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/Elouahabi-Naoufal/AirTrafficManager.svg?style=for-the-badge
[contributors-url]: https://github.com/Elouahabi-Naoufal/AirTrafficManager/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/Elouahabi-Naoufal/AirTrafficManager.svg?style=for-the-badge
[forks-url]: https://github.com/Elouahabi-Naoufal/AirTrafficManager/network/members
[stars-shield]: https://img.shields.io/github/stars/Elouahabi-Naoufal/AirTrafficManager.svg?style=for-the-badge
[stars-url]: https://github.com/Elouahabi-Naoufal/AirTrafficManager/stargazers
[issues-shield]: https://img.shields.io/github/issues/Elouahabi-Naoufal/AirTrafficManager.svg?style=for-the-badge
[issues-url]: https://github.com/Elouahabi-Naoufal/AirTrafficManager/issues
[license-shield]: https://img.shields.io/badge/License-All%20rights%20reserved-red.svg?style=for-the-badge
[license-url]: https://github.com/Elouahabi-Naoufal/AirTrafficManager
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/elouahabi-naoufal/
[java-shield]: https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white
[java-url]: https://openjdk.org/
[maven-shield]: https://img.shields.io/badge/Apache%20Maven-C71A36?style=for-the-badge&logo=apachemaven&logoColor=white
[maven-url]: https://maven.apache.org/
[mongodb-shield]: https://img.shields.io/badge/MongoDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white
[mongodb-url]: https://www.mongodb.com/
[javafx-shield]: https://img.shields.io/badge/JavaFX-FF711E?style=for-the-badge&logo=openjfx&logoColor=white
[javafx-url]: https://openjfx.io/
