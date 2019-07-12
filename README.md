# Estimation Project #

### Step 1: Sensor Noise ###
In this step, simulator is generated some noisy sensor data. Using notebook 
[Find_StdDev](
https://github.com/burkea/FCND-Estimation-CPP/blob/master/python/Find_StdDev.ipynb) calculate standart deviation of these sensor readings.

***Success criteria:*** *Your standard deviations should accurately capture the value of approximately 68% of the respective measurements.*

**Simulator Output:**

![](images/step_1_screenshot.png)

**Result:**

![Result](images/step_1_result.png)



### Step 2: Attitude Estimation ###

In this scenario we need to include IMU measurements to the result state. We need a better integration method that using the current attitude estimate (rollEst, pitchEst and ekfState(6)) to integrate the body rates into new Euler angles

I've used class Quaternion<float> with and methods FromEuler123_RPY and IntegrateBodyRate methods to implement it.

***Success criteria:*** *Your attitude estimator needs to get within 0.1 rad for each of the Euler angles for at least 3 seconds.*

**Simulator Output:**

![](images/step_2_screenshot.png)

**Result:**

![Result](images/step_2_result.png)



### Step 3: Prediction Step ###

Prediction can be break down into two parts: state mean and state covariance.

![](images/step_3_predict.png)
3.1: State Prediction
State transtion function as follows,
![](images/step_3_state_transition_function.png)
and Rotation matrix which is rotates body frame to global frame,
![](images/step_3_rotation_matrix.png)

attitude.Rotate_BtoI(<V3F>) function is used to rotate accelaration body frame to global frame.

**Simulator Output:**

![animation](images/step_3_1st_scenerio.gif)

3.2: Covariance Prediction
We should took the Jacobian of g for linear approximation,
![jacobian](images/step_3_jacobian.png)

and rbg_prime will calculate as follows:
![rgbprime](images/step_3_rotation.png)

and take both the calculate state covariance.

![covairance](images/step_3_covariance.png)


***Success criteria:*** *This step doesn't have any specific measurable criteria being checked.*


### Step 4: Magnetometer Update ###

In this step, the information from the magnetometer is added to improve filter's performance in estimating the vehicle's heading.

Magnetometer reports yaw in the global frame,
![](images/step_4_56_57.png)
 since this is linear, the derivative is a matrix of zeros and ones.
![](images/step_4_58.png)

***Success criteria:*** *Your goal is to both have an estimated standard deviation that accurately captures the error and maintain an error of less than 0.1rad in heading for at least 10 seconds of the simulation.*

After implemeting ```UpdateFromMag``` function then I've tuned QYawStd parameter to .5 and get 
```
PASS: ABS(Quad.Est.E.Yaw-0.000000) was less than Quad.Est.S.Yaw for 82% of the time
```
**Simulator Output:**

![](images/step_4_yaw_error.png)


### Step 5: Closed Loop + GPS Update ###

In this step, GPS update is implemented into estimator. position and velocity will get from GPS:

![](images/step_5_measurement.png)
and our model is 

![](images/step_5_measurement_model.png)

Then the partial derivative is the identity matrix:

![](images/step_5_jacobian.png)


implementation can be found in ```UpdateFromGPS``` function.

**Simulator Output:**
![](images/step_5.gif)

***Success criteria:*** *Your objective is to complete the entire simulation cycle with estimated position error of < 1m.*
**Result:**

```
PASS: ABS(Quad.Est.E.Pos) was less than 1.000000 for at least 20.000000 seconds
```


### Step 6: Adding Your Controller ###

In this step, I've replaced QuadController.cpp and QuadControlParams.txt with my own code, then ran `11_GPSUpdate`. After first failure, 30% decreasing position gains makes me pass.
**Simulator Output:**
![](images/step_6.gif)

***Success criteria:*** *Your objective is to complete the entire simulation cycle with estimated position error of < 1m.*
**Result:**

```
PASS: ABS(Quad.Est.E.Pos) was less than 1.000000 for at least 20.000000 seconds
```
