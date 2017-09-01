Speed selection and path planning of the ego-vehicle is treated seperately. Speed is controled with reference to lead vehicle distance and speed limit. Path planning model is explained below.

### 1. Transfer sensor fusion results to free space configuration
With ego-vehicle as the origin, the driving space is split into nine parts: 
* ego_space_front: space available within ego-lane in front of the car
* ego_space_rear: space available within ego-lane in the rear area of the car
* left_space_front: space available within left-lane in front of the car
* left_sapce_rear: space available within left-lane in the rear area of the car
* right_space_front: space available within right-lane in front of the car
* right_space_rear: space available within right-lane in the rear area of the car

### 2. Build the cost
The vehicle has 3 possible moves initially: lane stay, change to left, change to right.

In the program's definition, the move with the highest cost will be chosen. The cost building step is listed in the order as following:

* Initialize zero cost for all tree possible moves
```c++
double cost_left = 0.;
double cost_right = 0.;
double cost_stay = 0.;
```
* Check if lead vehicle is too close, check the current lane, offer extra cost for possible moves
```c++
if (too_close) {
  if (lane==0) {
    cost_right += 2;
  }
  else if (lane==1) {
    cost_left += 1;
    cost_right += 1;
  }
  else if (lane==2){
    cost_left += 2;
  }
} else {
  cost_stay += 2;
}
```
* Check left lane change & right lane change if it's available (cost > 0). Reset cost to zero if front or rear space is not big enough for lane change, otherwise, add extra cost using front space divided by a coefficient. (left example is shown below):
```c++
if (cost_left > 0 && (left_space_rear/20.)>1 && (left_space_front/20.)>1) {
  cost_left += (left_space_front/100.);
} else {
  cost_left = 0;
}
```
* Add extra cost to lane stay with front space devided by the same coefficient used in the last step
* Check all three costs, take the move with the highest cost.
