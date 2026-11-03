# Prerequisites

Make sure you are working in a session that has access to a display, as some examples launch GUIs.

## Linux (amd64 or arm64)

Make sure you have git, Python3, and zip compression tools installed:

```
sudo apt-get update && sudo apt-get install -y git unzip zip
```

Then download bazelisk and put it on your path:

```bash
mkdir -p ~/.local/bin
curl -Lo ~/.local/bin/bazel \
  https://github.com/bazelbuild/bazelisk/releases/latest/download/bazelisk-linux-$(dpkg --print-architecture)
chmod +x  ~/.local/bin/bazel
```

## MacOS Tahoe (arm64 only)

Install the system SDK frameworks:

```
xcode-select --install
```

Install Homebrew and then the necessary tools:

```bash
brew install bazelisk git
```

For high-precision timing and benchmarks on macOS, timer coalescing can introduce millisecond jitter into your execution loops. We recommend disabling it before running and time-critical code, and then enabling it again afterwards:

```
sudo sysctl -w kern.timer.coalescing_enabled=0
<run time critical code>
sudo sysctl -w kern.timer.coalescing_enabled=1
```

# Examples

Check out code:

```python
git clone https://github.com/asymingt/rcr-example.git
cd rcr-example
```

Provided you are on Mac or Linux, you will by default use a pre-warmed remote cache, and so all
build events should complete fairly quickly.

# Basic publisher and subscriber

Pubsub example with FastRTPS (default RMW):

```
bazel run @examples_rclcpp_minimal_publisher//:publisher_lambda
bazel run @examples_rclcpp_minimal_subscriber//:subscriber_lambda
```

![Basic pubsub example](images/rcr_example_pubsub.png)

# Basic publisher and subscriber with alternate RMW

Start up a zenohd router and then run a publisher and subscriber:

```
bazel run @rmw_zenoh_cpp//:zenohd
bazel run @examples_rclcpp_minimal_publisher//:publisher_lambda \
    --run_env=RMW_IMPLEMENTATION=rmw_zenoh_cpp
bazel run @examples_rclcpp_minimal_subscriber//:subscriber_lambda \
    --run_env=RMW_IMPLEMENTATION=rmw_zenoh_cpp
```

## Rviz2

Launch the Rviz2 robot simulator (needs a display)

```
bazel run @rviz2
```

![Basic pubsub example](images/rcr_example_rviz2.png)

## Moveit

Launch and end-to-end MoveIt example with a Universal Robot arm:

```
bazel run @ur_simulation_gz//launch:ur_sim_moveit_launch
```

https://github.com/user-attachments/assets/11c95021-06bb-4ff8-b059-645d902e3f2b

## Nav2

```
bazel run @nav2_bringup//launch:tb3_simulation_launch -- headless:=false
```

https://github.com/user-attachments/assets/acae149d-65d2-4c3e-8288-026dc91ff68b

# Statically compile a node


```
bazel build --config portable @examples_rclcpp_minimal_publisher//:publisher_lambda

```
