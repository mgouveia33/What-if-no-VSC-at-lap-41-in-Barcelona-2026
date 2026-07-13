# Barcelona 2026 Hamilton Final Stint Analysis

Lewis Hamilton's first victory for Ferrari in Barcelona was one of the team's most dominant wins in recent years, finishing $19.5$ seconds ahead of George Russell. After the team commitment to a three-stop strategy,  Hamilton's third stint played a critical role in allowing any strategic advantage to unfold. In contrast to Russell's older hard tyres, a fresh set of medium tyres allowed Hamilton to make significant gains over Russell. The plot below shows Hamilton's third stint between Lap $28$ and Lap $41$:

> You are catching really well, keep pushing, keep pushing! -  Excited Ferrari Engineer Team Radio on Lap $34$

<img width="1005" height="679" alt="image" src="https://github.com/user-attachments/assets/4845ebab-07ca-412a-bfcb-e66c9c6417e8" />

During Lap $41$, a Virtual Safety car caused by Alonso's malfunctioning car was the perfect opportunity for Ferrari to take their necessary last pit stop. Since the whole field moves away at a constant speed during the VSC period, a pitstop under these conditions comes much cheaper. The lucky factor was that the VSC occured presumably not far from Ferrari's planned pit stop window. 

Ferrari took the opportunity and secured Hamilton's first win. However, a lot of fans probably asked themselves the following question: 

### Was the VSC the main cause for the win? Or did it actually just further consolidated it?

In this mini project, we to attempt to answer this question by supposing that no VSC happened between Laps $41$ and $42$. We use Python's Fast F1 API [source](https://docs.fastf1.dev/) to acess Barcelona 2026 data in order to predict Hamilton and Russell laptimes under this hyphotetical situation.

## Overview and Results

<img width="1000" height="120" alt="image" src="https://github.com/user-attachments/assets/c5d6c1cd-26fb-4d94-80e2-ac6d98321171" />



Let $n$ be the hyphotetical lap number in which Hamilton goes for his last pit stop. 
  
Using Hamilton's acual third and fourth stint lap times, we perform linear regression on fuel-corrected lap times to simulate Hamilton's tyre degradation on Medium and Hard tyres, respectively.
  
With the coefficients provided by the linear model, we simulate Hamilton's lap time from Lap $39$ until Lap $n$, and from the pit stop at Lap $n$ until the end of the race. Using the same methods, we simulate Russell lap times on Hard tyres from Lap $39$ until the end of the race using his actual third stint lap times.
<p align="center">
<img width="576" height="453" alt="image" src="https://github.com/user-attachments/assets/937504ec-969c-4843-9dbf-ef1ac3c963f2" />
</p>


For Laps $63, 64, 65,$  we ignore the simulated lap times for both drivers and use actual lap times instead, since another VSC happened during that period and both drivers were at their last stint.

Using the simulated lap times and pit stop, we compute the total race time and the gap between Russell and Hamilton for each value of $n$. We provide a figure comparing the gap for  $n=40,41,42,43,44,45$ with the actual gap from the race:

<img width="1250" height="853" alt="image" src="https://github.com/user-attachments/assets/2640bbb0-e0cf-4057-8f1b-c439365a2c73" />





We did not pursue other values of $n$. We strongly believe that for $n>45$, the feasibility of lap time prediction using linear regression would be extrapolated, since Hamilton's medium tyre age would certainly be out of the range in which its degradation can be approximated by a linear model. 

#### From the plot, it is suggested that Hamilton would close the gap between Lap $54$ and $56$, with about $10$ laps remaining for the end of the race. The projected gap for the end of the race with no VSC on Lap 41 ranges between $7.5-9.5$ seconds. The actual VSC made the win comfortable, but the data suggests Hamilton had the pace to win without it.

The proposed analysis makes a choice of a single degradation tyre coefficient and base lap time for each stint coming from the Linear regression. These numbers are an estimate made from a limited number of laps and carry on uncertainties with them. As a result, every single simulated gap curve therefore hides how sensitive the conclusion is to inputs that are only approximately known.

## Methodology
We punctuate a few remarks in our assumptions:

1. Lap Time Simulation
- In order to perform linear regression to predict lap times, we ignore laps of a given stint that are not representative by the 107% rule.
- It is estimated that F1 cars lose about $0.03s$ per kg of fuel [source](https://www.stem.org.uk/resources/library/resource/25407/formula-one-race-strategy). We assummed that both Hamilton and Russell burned fuel in a linear fashion on every simulated lap time, ignoring engine maps and the eletric motor. Assuming a total of $70$ kg of fuel for both cars [source](https://www.formula1.com/en/latest/article/more-efficient-less-fuel-and-carbon-net-zero-7-things-you-need-to-know-about.ZhtzvU3cPCv8QO7jtFxQR), we obtain the fuel factor $f = 0.03*70/66 = 0.032$ seconds/lap.
- We fuel-correct lap times in order to estimate tyre degradation. The Fuel-corrected lap time $\overline{L}$ can be computed from the actual lap time $L$ as $\overline{L}_i = L_i - (66-i+0.5)*f$. Notice that the extra factor $0.5$ accounts for linear fuel burning.
- Let $b,m$ be the coefficients obtained from performing linear regression on fuel-corrected lap times of representative laps. Then, the simulated lap time $S$ is given by $S_i = b + m* age_i + (66 - i + 0.5)f,$ where $age_i$ stands for the tyre age at lap $i$.
- Lap $40$ had a Yellow flag that extended Hamilton actual gap significantly. We choose Lap 39 as the starting point for lap time simulation, since it was the latest lap in which both drivers lap times were not affected by extraordinary conditions.

 2. Pit Stop Simulation
  - It is estimated that a pit stop under green flag conditions at Barcelona makes a driver lose about $22.96$ seconds (including a 2.5 seconds change of tyres)  [source](https://www.formula1.com/en/latest/article/need-to-know-the-most-important-facts-stats-and-trivia-ahead-of-the-2026-barcelona-catalunya-grand-prix.51RmTAVS0jveoGWnBr9Ul). We added this constant to Hamilton's over all race time.
  - We assume that the constant $22.96$ takes into account the time lost in the pit lane during the in-lap and out-lap. Therefore, we can estimate the time loss due to tyre warm up at an out lap $i$ by the formula $warmup_i = L_i+L_{i-1} -(S_i + S_{i-1})- 22.96 $
 -  In order to better simulate Hamilton's out-lap on Hard tyres, we use the latest stint from every other driver on Hard tyres to compute an average warm up penalty. We removed any out-liers from the average warm up penalty computation by the 1.5 × IQR Rule. We added the warm up penalty to Hamilton's over all race time.


## Future discussions


A more robust exploration of this question is to perform Monte Carlo simulation: parameters are randomly chosen within their 95% confidence of interval for a large amount of simulations. By looking at the overall results for each choice of parameters, one could estimate the probability of a stop at lap $n$ leading to given positive gap by the end of the race.

The simulation lets Hamilton close on Russell without penalty. In reality, lap times rise in dirty air once within roughly one to two seconds, and an on-track pass costs additional time. A following penalty, or a cap on the closing rate, would make the projected late-race gaps conservative rather than optimistic.
