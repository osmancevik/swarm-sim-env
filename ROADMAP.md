# TEKNOFEST Swarm UAV Simulation Environment Roadmap

## Phase 1: Repository Setup and Initial Configuration
- [ ] **Git Initialization:** Initialize the git repository.
- [ ] **Directory Structure:** Define the core folder architecture (e.g., `docker/`, `scripts/`, `src/`, `config/`).
- [ ] **Gitignore:** Add a comprehensive `.gitignore` file specifically tailored for ROS 2, C++, Python, and Docker.
- [ ] **License:** Choose and add an open-source license (e.g., MIT or Apache 2.0).
- [ ] **Linting & Formatting:** Set up `.pre-commit-config.yaml` to enforce code quality (e.g., `black` for Python, `clang-format` for C++) from day one.

## Phase 2: Docker Environment Architecture and Base Images
- [ ] **Base Image Selection:** Choose a stable ROS 2 base image (e.g., `ros:humble-ros-base` for agents, `ros:humble-desktop` for the simulation).
- [ ] **Simulation World Dockerfile:** Draft a `Dockerfile.sim` for the main simulation container, including Gazebo and necessary headless dependencies.
- [ ] **Agent/Companion Dockerfile:** Draft a `Dockerfile.agent` simulating the companion computer (lightweight, minimal packages).
- [ ] **Entrypoint Scripts:** Create robust `entrypoint.sh` scripts for both containers to automatically source ROS 2 environments.
- [ ] **Docker Compose Foundation:** Create a baseline `docker-compose.yml` to orchestrate the simulation container and a test agent container.
- [ ] **Docker Networking Setup:** Define a custom Docker bridge network in the compose file to enable internal container-to-container communication.

## Phase 3: Headless Simulation World Container Setup (Gazebo)
- [ ] **Gazebo Dependencies:** Install Gazebo and the necessary ROS 2 integration plugins (`gazebo_ros_pkgs`) within `Dockerfile.sim`.
- [ ] **Arena World Design:** Create a custom `.world` file that mimics the TEKNOFEST competition environment (boundaries, visual elements, physics parameters).
- [ ] **Headless Launch Configuration:** Create ROS 2 launch files configured to start only the simulation server (`gzserver`) by default to save host resources, avoiding the GUI.
- [ ] **Physics Engine Tuning:** Optimize the simulation step size and real-time update rate to maximize CPU efficiency without losing sim2real physical accuracy.
- [ ] **Simulation Time Sync:** Configure the environment to publish the `/clock` topic, ensuring all future agent nodes are perfectly synced with simulation time.

## Phase 4: Agent Container Configuration and Resource Allocation
- [ ] **Multi-Agent Orchestration:** Expand the `docker-compose.yml` to support instantiating multiple agent containers (e.g., `uav_1`, `uav_2`).
- [ ] **Hardware Resource Limitation:** Apply strict CPU and RAM limits to each agent container (via Docker Compose `deploy.resources`) to perfectly simulate the real companion computer's constraints.
- [ ] **ROS 2 Namespacing:** Configure the entrypoints and launch files so each agent operates within a completely isolated ROS 2 namespace (e.g., `/uav1`) to prevent topic/service collisions.
- [ ] **Dynamic Environment Variables:** Pass dynamic startup parameters (e.g., `AGENT_ID`, starting positions, and target namespaces) to containers upon launch.
- [ ] **Code Volume Binding:** Set up volume mounts for the agent containers so teams can dynamically inject and test their custom swarm code without rebuilding the Docker image.

## Phase 5: Drone Dynamics, Sensors, and ROS Integration
- [ ] **UAV Model Integration:** Create or integrate a realistic quadcopter URDF/SDF model that matches the physical dimensions, weight, and inertia of the actual competition drones.
- [ ] **Flight Controller Interface:** Implement a Gazebo flight dynamics plugin (or integrate a SITL like PX4/ArduPilot) to accurately simulate the lower-level flight controller behavior.
- [ ] **Sensor Simulation & Noise:** Equip the UAV model with essential simulated sensors (IMU, GPS, Camera, Rangefinder) configured with realistic physical noise parameters.
- [ ] **ROS 2 Topic Bridging:** Configure `gazebo_ros` plugins to seamlessly expose sensor data, odometry, and TF trees to ROS 2, and listen for flight commands (e.g., `cmd_vel`).
- [ ] **Dynamic Spawning System:** Develop a launch script or ROS 2 node to dynamically spawn multiple UAV models into the headless Gazebo world at runtime based on the active agent containers.
- [ ] **Early YKİ (GCS) Integration:** Connect the custom Ground Control Station to the flawless ROS 2 network to visualize telemetry and monitor the headless Gazebo simulation before applying realistic network constraints.

## Phase 6: User Swarm Logic Integration Interface
- [ ] **Base Agent Template:** Create standard ROS 2 template classes (e.g., `BaseSwarmAgent` in Python/C++) that abstract away the underlying ROS 2 pub/sub plumbing for end-users.
- [ ] **Standardized Command API:** Define and expose a simplified, high-level API (topics/services/actions) for core operations like `takeoff()`, `land()`, `go_to_waypoint()`, and `get_state()`.
- [ ] **Reference Implementation:** Develop a functional example swarm application (e.g., a simple leader-follower or shape-formation script) to serve as a practical starting point for teams.
- [ ] **Virtual Safety & Geofencing:** Implement a virtual geofence and emergency stop (Kill Switch/RTL) mechanism that overrides user code, closely mimicking real TEKNOFEST safety procedures.

## Phase 7: Network Communication Simulation (ESP-NOW Mesh)
- [ ] **Message Payload Constraints:** Design a serialization layer (or custom ROS 2 message types) that strictly enforces the physical byte limits of the ESP-NOW protocol (e.g., max 250 bytes per packet).
- [ ] **Distance-Based Degradation:** Develop a network simulation node that calculates physical distances between agents in Gazebo and calculates realistic packet loss/latency probabilities.
- [ ] **Traffic Control Integration:** Utilize Linux Traffic Control (`tc` and `netem`) within the Docker containers to artificially apply the calculated latency, jitter, and bandwidth bottlenecks to the network interfaces.
- [ ] **Mesh Routing Logic:** Implement a lightweight peer-to-peer routing layer to simulate true decentralized mesh communication, ensuring agents don't "cheat" by using the flawless Docker bridge network.
- [ ] **YKİ (GCS) Telemetry Link:** Hook up the custom Ground Control Station (YKİ) so it receives telemetry and sends commands *only* through this simulated, restricted mesh network.

## Phase 8: Final Polish, Optimization, and Documentation
- [ ] **Docker Hub Publishing:** Set up a CI/CD pipeline (e.g., GitHub Actions) to automatically build and push the production-ready Docker images, allowing users to simply run `docker pull`.
- [ ] **Performance Profiling:** Conduct final stress tests to profile CPU and RAM usage, ensuring the headless sim maximizes host hardware efficiency.
- [ ] **Comprehensive README:** Write a highly detailed `README.md` containing the project vision, architecture diagrams, installation steps, and a quickstart guide.
- [ ] **Integration Tutorials:** Create a `docs/` directory with step-by-step tutorials on how external teams can inject their custom swarm algorithms into the `agent` containers.
- [ ] **Community & Contribution:** Add a `CONTRIBUTING.md` file, Code of Conduct, and Issue/PR templates to encourage open-source contributions from other TEKNOFEST teams.
- [ ] **First Stable Release:** Tag, document, and publish the official `v1.0.0` release of the simulation environment.
