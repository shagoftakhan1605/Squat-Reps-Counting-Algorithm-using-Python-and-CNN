# Real-Time Squat Rep Counting with MediaPipe Pose

This project counts squat repetitions from a webcam using MediaPipe Pose landmarks, records distances between body landmarks, and plots them over time.

* `Squat_Rep_Counting.ipynb`: real-time pose detection, distance recording and rep counting
* `squat_landmark_distances.txt`: distances recorded in one session (126 rows)
* `time_series_analysis.ipynb`: plots the recorded distances; a second cell contains a separate object-tracking demo (CSRT tracker)

Note: the rep counter uses pose landmarks and simple thresholds. No CNN is trained in this project, despite the repository name.

---

## Results

![3](https://github.com/user-attachments/assets/32ec32d0-ddb7-40e0-b103-34ca61c40895)
![2](https://github.com/user-attachments/assets/5114b10e-2cb0-43c9-83d2-30c4c3c14f37)
![1](https://github.com/user-attachments/assets/3ecc4c9d-4a66-4653-bc7d-16ecc10d977c)
![4](https://github.com/user-attachments/assets/d576ccd9-742c-423e-9a50-5d7548943aad)

---

## How It Works

1. **Pose detection:** MediaPipe Pose finds body landmarks in each webcam frame.
2. **Distances:** 2D distances (in normalised image coordinates) from the left ankle to the left knee, hip, elbow and eye are computed when those landmarks are visible.
3. **Recording:** a new row is written to `squat_landmark_distances.txt` whenever any distance changes by more than 0.01, so rows are not evenly spaced in time.
4. **Rep counting:** the vertical gap between the left knee and left hip is tracked. A rep starts when the gap drops below 0.05 (squat bottom) and is counted when it rises back above 0.15 (standing).

---

## Fix in This Version

The original counter used `hip.y - knee.y > 0.15`. Because MediaPipe's y axis points down, this value is negative while standing and only reaches about zero at full depth, so the counter never increased. The new version uses the knee-to-hip gap with separate down and up thresholds. Checked on a simulated three-rep movement: the new logic counts 3, the old logic counts 0.

---

## How to Run

```bash
pip install -r requirements.txt
jupyter notebook
```

1. Open `Squat_Rep_Counting.ipynb` and run the cell. Stand side-on to the webcam so your left leg is visible. Press `q` to stop.
2. Open `time_series_analysis.ipynb` and run the first cell to plot the recorded distances.

---

## Limitations

* Thresholds are fixed and may need adjusting for camera distance and angle
* Only the left side of the body is used
* No timestamps are saved, so the plots show recording order, not real time
* Squat form (correct vs faulty) is not assessed automatically; the plots are for visual inspection

---

## Tools

Python, MediaPipe, OpenCV, NumPy, pandas, Matplotlib
