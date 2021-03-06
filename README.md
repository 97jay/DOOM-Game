# DOOM-Game

Objective:
The objective of this project is to develop an autonomous agent to perform well in
the first person shooting games using various reinforcement learning techniques.

Introduction of Environment:
The first-person shooting game is a partially observable environment in which we
have an AI agent that plays deathmatch using only the pixels on the screen. The
state is represented by the most recent frame of height 60 pixels and width 108
pixels and we have actions depending upon the type of game we are playing. The
common actions are move forward, move left, move right and attack. The main
objective of the agent is to collect as many rewards as possible such as we give
positive reward for object pickup (health, weapons and ammo), negative reward
for losing health (attacked by enemies or walking on lava) and negative reward
for shooting or losing ammo.

Environment used:
In this project we have used the DOOM game to implement the reinforcement
learning algorithm. So DOOM has ViZDoom 1 as its environment, which we
have used in this project. So, ViZDoom allows developing AI bots that play
Doom using only the visual information (the screen buffer). There are several
scenarios that come with VizDoom. So, we chose the Defend the Center scenario
since it provides a challenging 3D partially observable environment. In this
scenario, the circular arena is occupied by the agent. In this scenario you can see
that enemies continuously spawn from far away and gradually move closer to the
agent until they are close enough to attack from close range. The agent is
equipped with a handgun. We only have 26 bullets and limited health, its
objective is to eliminate as many enemies as possible while avoiding being
attacked and killed. By default, a death penalty of -1. To provide more perspective
to it, we enriched the variety of rewards to include a +1 reward for every kill, and
a -0.1 reward for losing ammo and health. We find that giving such rewards seem
to be a good trick to get the agent to learn good policies.
It is primarily intended for research in machine visual learning, and deep
reinforcement learning, in particular. Following are the features of VizDoom-:

● Multi-platform
● API for Python, C++, Lua, Java
● Easy-to-create custom scenarios
● Async and sync single-player and multiplayer modes
● Fast (up to 7000 fps in sync mode, single threaded)
● Lightweight (few MBs)
● Customizable resolution and rendering parameters
● Access to the depth buffer (3D vision)
● Automatic labeling game objects visible in the frame

Dependencies required:
So to run the model, following are the dependencies which are required:
1. Python 2/3
2. Numpy and OpenCV
3. VizDoom

Scenario:
So there are various maps/scenarios in the DOOM game, but we chose the
“Defend the center” scenario. So in this scenario the agent is in the middle of a
circular map. Monsters are regularly appearing on the sides and are walking
towards the agent. The agent is given a pistol and limited ammo, and must turn
around and kill the monsters before they reach it.

Q Learning vs Policy Gradients:
From the given consensus you can see what comparison we have made between
Q-learning and Policy gradient.

Policy Gradient is mostly used for the application of wider range problems. For
some instances, where the Q function is too complex to be learned, DQN will fail
miserably. On the other hand, Policy Gradients outperforms DDQN and is still
capable of learning a good policy since it directly operates in the policy space.
Adding further to it, Policy Gradients usually show faster convergence rate than
DQN, but have a tendency to converge to a local optimal. So Policy Gradients is
capable of learning stochastic policies, since it models the probabilities of actions.
While DQN can’t do this. Also, Policy Gradients can be easily applied to model
continuous action space since the policy network is designed to model probability
distribution, on the other hand, DQN has to go through an expensive action
discretization process which is undesirable.

So in the above paragraph we have mentioned about the benefits of the Policy
gradient, now let’s discuss why we can’t always use it or what are it’s
disadvantages. So, one of the biggest drawbacks of Policy Gradients is the high
variance in estimating the gradient of E[R t ]. Essentially, when we perform a
gradient update, we are using an estimation of gradient generated by a series of
data points <s,a,r,s> which are accumulated through a single episode of game
play. This is known as the Monte Carlo method. Eventually, the estimation can be
very noisy, and a bad gradient estimate could adversely impact the stability of the
learning algorithm. In contrast, when DQN does work, it usually shows a better
sample efficiency and more stable performance.

Algorithms Used:
We’ve implemented DDQN, REINFORCE and A2C to defend the center
scenario. We used similar neural network architectures to represent the Q function
network and Policy network respectively. We ran 20,000 episodes for each
method and used moving average kill counts (average on 50 episodes) as the
metric to probe performance. Following are the parameters that we have taken to
construct the model-:
1. General parameters-:
● freedoom
● height 60
● width 108
● gray "false"
● use_screen_buffer "true"
● use_depth_buffer "false"
● labels_mapping ""
● game_features "target,enemy"
2. Actions/Action Space-:
Action combinations contain, looking in all 4 directions and
shooting(attacking)


Double Deep Q-Network(DDQN):
Double DQN uses the existing architecture and deep neural network of the DQN
algorithm without requiring additional networks or parameters. Following are the
parameters which are important for the DDQN-:
1. Sampling a batch of transitions from the replay buffer.
2. Using the next states from the sampled batch, run the online network in
order to find the Q maximizing action argmaxQ(st+1,a) . For these actions,
use the corresponding next states and run the target network to calculate
Q(st+1,argmax a Q(st+1,a)) .
3. In order to vanish the updates for the actions that were not used, in this
case we can use the current states from the sampled batch, and run the
given network to get the current Q values predictions. Set those values as
the targets for the actions that were not really used.
4. For each action that was played, use the following equation for calculating
the targets of the network: yt=r(st,at)+γ⋅Q(st+1,argmax a Q(st+1,a))
5. Finally, train the given network using the current states as inputs, and with
the given target values.
6. After every n steps, copy the weights from the given network to the target
network.

REINFORCE:
This is a policy gradient algorithm called REINFORCE. It is a Monte Carlo based
policy gradient approach. The policy parameter θ is updated based on the
estimated return by the Monte Carlo method and is updated using the stochastic
gradient ascent.The update is proportional to the return G t , the gradient of the probability of
taking the action actually taken and is divided by the probability of taking that
action. The algorithm works as follows:

1. First we initialize the policy parameter θ at random.
2. Generate a trajectory on policy π θ : S 1 , A 1 , R 1 , S 2 , A 2 , …, S Tot .
3. For t=1, 2, …, T:
A. Estimate the the return G t ;
B. Update policy parameters: θ←θ+αγ t G t ∇ θ log π θ (At|St)
Using the above equations and the update rule we get to an optimal policy that
maximizes the reward.

Advantage Actor Critic (A2C):
In the previous algorithm REINFORCE we saw that it is based on Monte Carlo
updates. Due to this there is high variability in log probabilities and the
cumulative rewards will make noisy gradients and cause unstable learning thus
can lead to unstable policy. Intuitively, making the cumulative reward smaller by
subtracting it with a baseline will make gradients smaller, and thus smaller and
more stable updates.
Now we are going to see the Advantage Actor Critic method. In general, in a
policy gradient there are two components namely policy model and the value
function. We can make use of value function in addition to policy as it can assist
the policy update and this is what Advantage Actor Critic method does. In this we
have two models. One of them is the critic model which updates the value
function or action value function parameters w, i.e., V(s) or Q(s,a) and the actor
updates the policy parameters θ for πθ(a|s), in the direction suggested by the
critic.
In A2C we use the V function as the baseline function and subtract this value
from the Q value. It means that we find out how much better it is to take a
specific action compared to the average, general action at the given state.This
value is called the advantage value.But this means that we have to construct two neural 
networks for the Q value and the V value which makes it inefficient.
We just have to use one neural network for the V function thus making it
efficient.


Experiment:
We have implemented DDQN, REINFORCE and A2C on VizDoom Defend the
Center scenario with Keras framework. So, to have a fair comparison, we used
similar neural network architectures to represent the Q function network and
Policy network respectively. Hence we ran the 20k episodes for each algorithm,
and calculated average kill counts on every 50 episodes, as the metric to the
performance.

Result
The results are with respect to the graph plotted below. This graph shows you the
kill counts against the number of episodes played. Firstly, if you observe, the
results given by the DDQN are comparatively better than that of the Policy
Gradient methods. The DDQN was able to converge to a good policy in the first
1500 episodes, while the REINFORCE and A2C algorithms took almost 4000 to
5000 epsides to converge. This could be due to the fact that DDQN has higher
data efficiency than the Policy Gradient methods. We require a lot of data to get
the Policy Gradients to work better. Hence if we have to work on a limited
amount of data we should probably not choose Policy Gradient methods as you
won't see much difference. One more interesting thing to observe is that DDQN
had a very smooth and a stable performance that improved steadily over time,
while A2C’s performance fluctuated from episode to episode. However, despite
its variance, A2C was able to reach a higher maximum average kills (around 12)
than DDQN (around 11) over the entire time. When you compare the
REINFORCE with the A2C algorithm the former could not get much kills when
compared with the latter despite the fact that the former is Monte Carlo based. We
think its because the Monte Carlo method goes into local minima and that's why it
could not give better results

Furthermore, it’s interesting to point out that DDQN and A2C had independently
discovered 2 different strategies to play the game.
We observe that A2C agent learned to turn only in one direction (i.e. Left) and did
not shoot the targets which were far away. In contrast, the DDQN agent wiggled a
lot and tried to shoot whenever an enemy was within its sight, irrespective of its
distance. We have attached GIFs of both the DDQN and the REINFORCE
algorithm in our folder, to give you an idea of how both the algorithms learned to
play. We think the strategy adopted by the Policy Gradients agent makes more
sense and is more similar to the way every normal person would approach the
game. It would be better to shoot from close range to avoid missing the target and
waste the ammos. On the whole we feel that A2C and DDQN performed better
than the REINFORCE algorithm. 

Conclusion :
From this implementation we were able to learn a lot more about the Q Networks
and the Policy Gradient methods. It was very challenging to work on the
VizDoom Defend the Center scenario and we could have scaled this to much
more complex environments if we had more time and resources. These methods
in general exhibit better results than the naive Q-Learning. To conclude we can
say that Policy Gradient methods are in general a better way to move forward as
they can be used for complex environments due to the fact that there is more data
to work on and it can give very good results than the Value based methods. In
future we can try more complex algorithms like DDPG, PPO and even Recurrent
Neural Networks, and we can find out how good they work in these
environments. Overall it was a very good and challenging experience to work in
a different environment rather than working on the Open