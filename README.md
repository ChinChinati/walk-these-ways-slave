## Training a Model <a name="simulation"></a>

### Installation <a name="installation"></a>

#### Install pytorch 1.10 with cuda-11.3:

```bash
pip3 install torch==1.10.0+cu113 torchvision==0.11.1+cu113 torchaudio==0.10.0+cu113 -f https://download.pytorch.org/whl/cu113/torch_stable.html
```

#### Install Isaac Gym

1. Download and install Isaac Gym Preview 4 from https://developer.nvidia.com/isaac-gym
2. unzip the file via:
    ```bash
    tar -xf IsaacGym_Preview_4_Package.tar.gz
    ```

3. now install the python package
    ```bash
    cd isaacgym/python && pip install -e .
    ```
4. Verify the installation by try running an example

    ```bash
    python examples/1080_balls_of_solitude.py
    ```
5. For troubleshooting check docs `isaacgym/docs/index.html`

#### Install the `go1_gym` package

In this repository, run `pip install -e .`

### Verifying the Installation

If everything is installed correctly, you should be able to run the test script with:

```bash
python scripts/test.py
```

The script should print `Simulating step {i}`.
The GUI is off by default. To turn it on, set `headless=False` in `test.py`'s main function call.

### Environment and Model Configuration <a name="configuration"></a>


**CODE STRUCTURE** The main environment for simulating a legged robot is
in [legged_robot.py](go1_gym/envs/base/legged_robot.py). The default configuration parameters including reward
weightings are defined in [legged_robot_config.py::Cfg](go1_gym/envs/base/legged_robot_config.py).

There are three scripts in the [scripts](scripts/) directory:

```bash
scripts
├── __init__.py
├── play.py
├── test.py
└── train.py
```

You can run the `test.py` script to verify your environment setup. If it runs then you have installed the gym
environments correctly. To train an agent, run `train.py`. To evaluate a pretrained agent, run `play.py`. We provie a
pretrained agent checkpoint in the [./runs/pretrain-v0](runs/gait-conditioned-agility/pretrain-v0) directory.



### Training and Logging <a name="training"></a>

To train the Go1 controller from [Walk these Ways](https://sites.google.com/view/gait-conditioned-rl/), run: 

```bash
python scripts/train.py
```

After initializing the simulator, the script will print out a list of metrics every ten training iterations.

Training with the default configuration requires about 12GB of GPU memory. If you have less memory available, you can 
still train by reducing the number of parallel environments used in simulation (the default is `Cfg.env.num_envs = 4000`).

To visualize training progress, first start the ml_dash frontend app:
```bash
python -m ml_dash.app
```
then start the ml_dash backend server by running this command in the parent directory of the `runs` folder:
```bash
python -m ml_dash.server .
```

Finally, use a web browser to go to the app IP (defaults to `localhost:3001`) 
and create a new profile with the credentials:

Username: `runs`
API: [server IP] (defaults to `localhost:8081`)
Access Token: [blank]

Now, clicking on the profile should yield a dashboard interface visualizing the training runs.

### Analyzing the Policy <a name="analysis"></a>

To evaluate the most recently trained model, run:

```bash
python scripts/play.py
```

The robot is commanded to run forward at 3m/s for 5 seconds. After completing the simulation, 
the script plots the robot's velocity and joint angles.

The GUI is on by default. 
If it does not appear, and you're working in docker, make sure you haven't forgotten to run `bash docker/visualize_access.bash`.
