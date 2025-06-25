Reinforcement learning (RL) is an area of machine learning that focuses on how learning from interacting with an environment can occur effectively for intelligent agents.
The goal of RL problems is to learn which actions to take and when to take them to maximize cumulative reward over time. 
This differs from supervised learning, as the decision maker is NOT TOLD what is the correct action in the current situation and instead must learn from the consequences of its own actions. 
It is also different from unsupervised learning, as the goal is not to find structure in unlabeled data but instead to maximize reward.

In reinforcement learning problems, there is an <agent> who makes decisions and learns how to achieve a goal. This agent interacts with the <environment> by taking <actions>. The environment produces <reward>s as a result of the actions taken, and these rewards provide insight into the overall value of taking an action in a particular state. Actions may affect the environment, leading to changes in state, which the agent observes.
This mapping from states to actions governing the agent's behavior is called a <policy>. The agent also learns a value function which it uses to estimate how desirable the states are, when behaving according to the policy.

Typically, the agent interacts with the environment over time. At each time step, an action is taken based on the agent’s policy, a reward is obtained, and the next state is observed. The sequence of transitions experienced by the agent starting from the intitial state until the final state is called a trial or an <episode>.
So at every timestep a reward is obtained and this reward information is stored in what is called a Q-value, which reflects the expected reward for each action based on the history of experienced rewards.

Exploiting the enviroment:
Now imagine that you have a robot in an enviroment with 2 trees, one that gives more fruit but it's further from the robot initial position and the other one that is closer but returns less fruit. If the robot strictly follows its current policy, it will always move to the closer tree. This is called <exploitation>, as the robot exploits its knowledge when deciding how to pursue rewards. The robot needs to explore in order to reduce the uncertainty about the further tree, in order to learn that it actually yields greater rewards. This introduces an important concept in reinforcement learning: "the explore-exploit dilemma". Agents must balance exploiting known rewards with exploring the environment, and potentially discovering more rewarding actions.

Constantly performing the action that the robot believes yields the greatest reward is called being greedy. This does not allow the robot to explore at all, and the robot will never discover that the other tree yields greater rewards. To allow for some exploration, the robot can select actions according to epsilon-greedy policy. This is a simple way to balance exploration and exploitation, by exploring (i.e. choosing action randomly) with probability epsilon and exploiting by greedily following the policy with probability 1-epsilon. Increasing epsilon increases exploration, setting epsilon to zero will result in a greedy policy.

Present vs Future gratification:
Humans often balance short-term gratification and long-term consequences in their decision-making. We are predisposed to value rewards received now more than those received in the future. This makes sense as the distant future is more uncertain than the present. Similarly, the agent needs to find a balance between acquiring immediate rewards and considering long-term gain. This is why <discounted cumulative rewards> are used.
#AI 
[[Cognitive Computing Summary]]
[[Neural Networks]]
