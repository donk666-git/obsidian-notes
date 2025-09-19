discriminative models 判别式模型

## definition
A computer program is said to learn from experience E with respect to some class of tasks T and performance measure P, if its performance at tasks in T, as measured by P, improves with experience E.” -- Tom M. Michell, 1997

- experience
- tasks
- performance measure

## attack types
***adversarial attacks
poisoning attacks
membership attack
model inversion
model extraction

1.2. they both want induce the wrong prediction from the model
however, 
1. manipulate the inference period by creating spam input to bypass the detection
2.temper with the training data by remove the spam from its folder to inbox

privacy violation:
3. to ensure whether a email was used to train the model
4. reconstruct the local model that resemble to the one


## attack taxonomy

perturb 打乱
adversarial perturbation

optimization 最优化