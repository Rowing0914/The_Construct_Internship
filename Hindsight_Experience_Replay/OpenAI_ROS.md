## OpenAI_ROS

The *openai_ros* package provides a common structure for organizing everything you need to create your robot training from zero, requiring very little implementation. Overall, the repo is being organised as follows;

1. Training Environments: Compatible with OpenAI Gym env
   1. Task Environment: specify the task where a robot works on
   2. Robot Environment: specify the robot which learns to execute the task defined above
   3. Gazebo Environment: secure the connection and simulation on Gazebo
2. Training Scripts: RL algorithms



## Details of Environments

It is basically composed of the following elements.

- Gazebo Environment(inherits from OpenAI Gym)

  - step: applies the selected action to a robot and returns the info regarding the env
  - seed: set the randomness of some parameters
  - reset: reset the env
  - close: terminates the env

- Available `Robot_name` and `Task_name` Environment: As of 6/4/2019

  - cartpole: cartpole_stay_up
  - cube: moving_cube
  - fetch: fetch, fetch_reach
  - hopper: hopper
  - husarion: husarion
  - iriwam: iriwam
  - parrotdrone: parrotdrone
  - sawyer: sawyer
  - shadow_tc: shadow_tc
  - sumitxl: sumitxl
  - turtlebot2: turtlebot2
  - turtlebot3: turtlebot3
  - wamv: wamv

  

## Documentation of APIs

- doc
- msg
  - [RLExperimentInfo.msg](https://bitbucket.org/theconstructcore/openai_ros/src/b5fb3523a4c089a2bcbc4d7db9ed7453e227c372/openai_ros/msg/RLExperimentInfo.msg?at=kinetic-devel): define the basic message format
    - episode_number
    - episode_reward
- stc/openai_ros
  - robot_envs
  - task_envs
  - [robot_gazebo_env.py](https://bitbucket.org/theconstructcore/openai_ros/src/b5fb3523a4c089a2bcbc4d7db9ed7453e227c372/openai_ros/src/openai_ros/robot_gazebo_env.py?at=kinetic-devel): defines `RobotGazeboEnv` to make an environment for RL agent to work on
    - **Important library**: [gym(OpenAI Gym)](<https://github.com/openai/gym>)
    - seed: using `gym.utils.seeding`, we set the seed of randomness(stochasticity)
    - step: given an action, we move one time-step forward in the env and returns the info(`obs, reward, done, info`) regarding the env
    - reset: reset the env or task and returns `obs`
    - close: terminates the task or env
    - _update_episode_publish_reward_topic: returns the cumulative reward in an episode and increment an episode number
    - _reset_sim: resets a simulation using a `GazeboConnection` API
    - _set_init_pose: Sets the Robot in its init pose
    - _check_all_systems_ready: Checks that all the sensors, publishers and other simulation systems are ready
    - _get_obs: Returns the observation
    - _init_env_variables: initialises the variables in a simulation
    - __set_action: applies given action in a simulation
    - _is_done: indicates if an episode has been done
    - _compute_reward: calculates a reward in an episode based on an observation
    - _env_step: initialise the env following the configuration
  - [robot_gazebo_env_goal.py](https://bitbucket.org/theconstructcore/openai_ros/src/b5fb3523a4c089a2bcbc4d7db9ed7453e227c372/openai_ros/src/openai_ros/robot_gazebo_env_goal.py?at=kinetic-devel): same as `robot_gazebo_env.py`
  - [gazebo_connection.py](https://bitbucket.org/theconstructcore/openai_ros/src/b5fb3523a4c089a2bcbc4d7db9ed7453e227c372/openai_ros/src/openai_ros/gazebo_connection.py?at=kinetic-devel): defines `GazeboConnection` class to access essential properties of Gazebo
    - **Important library**: [gazebo_msgs](<http://wiki.ros.org/gazebo_msgs>)
    - pauseSim: call `/gazebo/pause_physics` to pause the simulation
    - unpauseSim: call `/gazebo/pause_physics` to unpause the simulation
    - resetSim: call either `/gazebo/reset_world`(for World) or`/gazebo/reset_simulation`(for Simujlation) to reset them
    - resetSimlation: call `/gazebo/reset_simulation` to restart the simulation
    - resetWorld: being called by `resetSim()` to reset the Gazebo world
    - init_values: calls `init_physics_parameters()` to initialise the values
    - init_physics_parameters: initialise the params, e.g., like gravity, friction coefficients etc..
    - update_gravity_call: pause the simulation and change the gravity followed by restarting
    - change_gravity: [change the gravity params in a simulation environment](<http://answers.gazebosim.org/question/14535/set-gravity-on-each-object-gazebo7/>)
  - [controllers_connection.py](https://bitbucket.org/theconstructcore/openai_ros/src/b5fb3523a4c089a2bcbc4d7db9ed7453e227c372/openai_ros/src/openai_ros/controllers_connection.py?at=kinetic-devel): defines `ControllersConnection`to turn them on and off given the available controller list
    - **Important library**: [controller_manager_msgs](<http://wiki.ros.org/ros_control>)
    - switch_controllers: switch the active-controller and non-active one
    - reset_controllers: activate and deactivate the controller
    - update_controllers_list: update the controller list
- templates