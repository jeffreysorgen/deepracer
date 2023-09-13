# deepracer
This is the time trial for the model I created in 2019. It's not not good but it goes completely around the track, which is a big step. 

<img src="/deep-racer-time-trial.gif"/>

I cut off a few seconds using a better model (also 2019). The track was the same size and shape.

<img src="/better-lap-time.png" width="45%"/>

## Reward function:
Tweaking these parameters would help. 
``` python
def reward_function(params):
    ###############################################################################
    '''
    Example of using waypoints and heading to make the car in the right direction
    '''

    import math

    # Read input variables
    waypoints = params['waypoints']
    closest_waypoints = params['closest_waypoints']
    heading = params['heading']
    speed = params['speed']
	
    all_wheels_on_track = params['all_wheels_on_track']
    steering = params['steering_angle']
    is_left_of_center = params['is_left_of_center']
	
	# Initialize the reward with typical value 
    reward = 1.0
    #speed is good
    reward *= (1+speed/2)

    if not all_wheels_on_track: 
		#inside lane
    	if steering <0 and is_left_of_center or steering >0 and not is_left_of_center :
	    	reward *= (1+speed/10)

    # Calculate the direction of the center line based on the closest waypoints
    next_point = waypoints[closest_waypoints[1]]
    prev_point = waypoints[closest_waypoints[0]]

    # Calculate the direction in radius, arctan2(dy, dx), the result is (-pi, pi) in radians
    track_direction = math.atan2(next_point[1] - prev_point[1], next_point[0] - prev_point[0]) 
    # Convert to degree
    track_direction = math.degrees(track_direction)

    # Calculate the difference between the track direction and the heading direction of the car
    direction_diff = abs(track_direction - heading)
    if direction_diff > 180:
        direction_diff = 360 - direction_diff

    # Penalize the reward if the difference is too large
    DIRECTION_THRESHOLD =  5.0 #8.0 #9.0 #10.0
    if direction_diff > DIRECTION_THRESHOLD:
        reward *= 0.5

    return reward
```
