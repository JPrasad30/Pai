AI 

Practical - 01
Aim: Implementing advanced deep learning algorithms such as convolutional neuralnetworks (CNNs) or recurrent neural networks (RNNs) using Python librarieslike TensorFlow or PyTorch.

from keras.datasets import mnist
from keras.layers import Dense, Conv2D,Flatten
from keras.models import Sequential
import matplotlib.pyplot as plt
from google.colab import drive
drive.mount('/content/drive')
(train_images, train_labels), (test_images, test_labels)= mnist.load_data()
train_images.shape
test_images.shape
plt.imshow(train_images[1])
plt.imshow(test_images[1])
test_labels[1]
train_image,test_images=train_images/255.0,test_images/255.0
import numpy as np
train_images=train_images.reshape(60000,28,28,1)
test_images=test_images.reshape(10000,28,28,1)
from keras.utils import to_categorical
train_labels=to_categorical(train_labels)
test_labels=to_categorical(test_labels)
test_labels[1]
train_labels[1]
cnnmodel=Sequential()
cnnmodel.add(Conv2D(32,(3,3),activation='relu',input_shape=(28,28,1)))
cnnmodel.add(Conv2D(64,(3,3,),activation='relu'))
cnnmodel.add(Flatten())
cnnmodel.add(Dense(10,activation='softmax'))
cnnmodel.compile(optimizer='adam',loss='categorical_crossentropy',metrics=['accuracy'])
cnnmodel.fit(train_images,train_labels,epochs=3)
predictions=cnnmodel.predict(test_images)
predictions[1]
#import numpy as np
for i in range(3):
print(predictions[i])
print(test_labels[i])

OUTPUT
 

Practical - 02
Aim: Building a natural language processing (NLP) model for sentiment analysis or text classification

from sklearn.datasets import fetch_20newsgroups
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.naive_bayes import BernoulliNB,MultinomialNB
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
newsgroup=fetch_20newsgroups(subset='all')
newsgroup.target_names
vectorizer1=CountVectorizer(binary=True)
vectorizer2=CountVectorizer(binary=False)
x1=vectorizer1.fit_transform(newsgroup.data)
x2=vectorizer2.fit_transform(newsgroup.data)
y=newsgroup.target
print(y)
xtrain1,xtest1,ytrain,ytest=train_test_split(x1,y,test_size=0.25,random_state=42)
xtrain2,xtest2,ytrain,ytest=train_test_split(x2,y,test_size=0.25,random_state=42)
bnb=BernoulliNB()
mnb=MultinomialNB()
bnb.fit(xtrain1,ytrain)
mnb.fit(xtrain2,ytrain)
y_pred1=bnb.predict(xtest1)
y_pred2=mnb.predict(xtest2)
accuracy_score(ytest,y_pred1)
accuracy_score(ytest,y_pred2)

OUTPUT
 















Practical - 03

AIM: Developing a recommendation system using collaborative filtering or deep learning approaches
import pandas as pd
ratings = pd.read_csv('BX-Book-Ratings.csv',encoding="latin-1",delimiter=";")
ratings
books=pd.read_csv('BX-Books (2).csv', encoding="latin-1")
books
ratings_books=pd.merge(ratings,books,on="ISBN")
ratings_books
ratings_books_sample=ratings_books.sample(frac=0.5,random_state=1)
ratings_books_sample
ratings_books_sample.to_csv('ravi.csv')
ratings_books_sample.columns
ratings_books_pivot=ratings_books_sample.pivot_table(index='Book-Title',columns='User-ID',values='Book-Rating').fillna(0)
ratings_books_pivot
from sklearn.neighbors import NearestNeighbors
model=NearestNeighbors(metric='cosine',algorithm='brute',n_neighbors=7, n_jobs=-1)
model.fit(ratings_books_pivot)
ratings_books_pivot.loc[["Cracking the Da Vinci Code : The Unauthorized Guide to the Facts Behind Dan Brown's Bestselling Novel"]]
#indices=model.kneighbors(ratings_books_pivot.loc[['Flesh Tones: A Novel']])
#indices
indices=model.kneighbors(ratings_books_pivot.loc[["Crossing Over"]])
indices
neighbor_indices = indices[1][0]
recommendations = ratings_books_pivot.index[neighbor_indices]
print(recommendations)

OUTPUT

 











Practical - 04
AIM: Applying reinforcement learning algorithms to solve complex decision-making problems
Code:
import numpy as np
import random
# Define the environment
grid_size = 3 # Smaller grid
goal_state = (2, 2)
obstacle_state = (1, 1) # Single obstacle
actions = ['up', 'down', 'left', 'right']
action_to_delta = {
'up': (-1, 0),
'down': (1, 0),
'left': (0, -1),
'right': (0, 1)
}
# Initialize Q-table (simple 3D array for states and actions)
q_table = np.zeros((grid_size, grid_size, len(actions)))
# Parameters
alpha = 0.1 # Learning rate
gamma = 0.9 # Discount factor
epsilon = 1.0 # Exploration rate
epsilon_decay = 0.99
min_epsilon = 0.1
episodes = 200 # Fewer episodes
# Reward function
def get_reward(state):
if state == goal_state:
return 10 # Reward for reaching the goal
elif state == obstacle_state:
return -10 # Penalty for hitting the obstacle
return -1 # Step penalty
# Check if the new state is valid
def is_valid_state(state):
return 0 <= state[0] < grid_size and 0 <= state[1] < grid_size and state != obstacle_state
# Main Q-learning loop
for episode in range(episodes):
state = (0, 0) # Start at the top-left corner
total_reward = 0
while state != goal_state:
# Choose an action (epsilon-greedy
strategy) if random.uniform(0, 1) < epsilon:
action = random.choice(actions) # Explore
else:
action = actions[np.argmax(q_table[state[0], state[1]])]  # Exploit best action
# Perform the action
delta = action_to_delta[action]
next_state = (state[0] + delta[0], state[1] + delta[1])
# Stay in the same state if the move is invalid
if not is_valid_state(next_state):
next_state = state
# Get reward and update Q-table
reward = get_reward(next_state)
total_reward += reward
best_next_action = np.max(q_table[next_state[0], next_state[1]])
q_table[state[0], state[1], actions.index(action)] += alpha * (
reward + gamma * best_next_action - q_table[state[0], state[1], actions.index(action)]
)
# Update state
state = next_state
# Decay epsilon
epsilon = max(min_epsilon, epsilon * epsilon_decay)
print(f"Episode {episode + 1}: Total Reward = {total_reward}")
# Display the learned policy
policy = np.full((grid_size, grid_size), ' ')
for i in range(grid_size):
for j in range(grid_size):
if (i, j) == goal_state:
policy[i, j] = 'G' # Goal
elif (i, j) == obstacle_state:
policy[i, j] = 'X' # Obstacle
else:
best_action = np.argmax(q_table[i, j])
policy[i, j] = actions[best_action][0].upper()  # First letter of the best action
print("Learned Policy:")
print(policy)

OUTPUT
 







Practical - 05

AIM: Building a deep learning model for time series forecasting or anomaly detection
!pip install pycaret &>/dev/null
print("pycaret install sucessful")
from pycaret.utils import version
version()
from pycaret.datasets import get_data
from pycaret.classification import setup,compare_models,finalize_model,predict_model
data=get_data("diamond")
data.shape
data.info()
dataset=data.sample(frac=0.9,random_state=786)
dataset
data_unseen=data.drop(dataset.index)
dataset.shape,data_unseen.shape
dataset.reset_index(drop=True,inplace=True)
dataset
data_unseen.reset_index(drop=True,inplace=True)
data_unseen
from pycaret.regression import*
s=setup(data=dataset,target='Price',session_id=120)
best=compare_models()
final_model=create_model('xgboost')
print(final_model)
predict_model(final_model,data=data_unseen)

OUTPUT
 






Practical - 06
Aim: Implementing a machine learning pipeline for automated feature engineering and model selection.
import numpy as np
import matplotlib.pyplot as plt
from tensorflow.keras.models import Model
from tensorflow.keras.layers import Input,Dense
normal_data = np.random.normal(0,1,(1000,2))
anomalous_data = np.random.uniform(5,10,(100,2))
normal_data[:3]
anomalous_data[:3]
len(normal_data),len(anomalous_data)
data=np.vstack([normal_data,anomalous_data])
labels=np.array([0] * len(normal_data) + [1] * len(anomalous_data))
data.shape
labels
labels.shape
def build_autoencoder(input_dim):
input_layer=Input(shape=(input_dim,))
encoded=Dense(4,activation='relu')(input_layer)
encoded=Dense(2,activation='relu')(encoded)
decoded=Dense(4,activation='relu')(encoded)
output_layer=Dense(input_dim,activation='linear')(decoded)
autoencoder=Model(inputs=input_layer,outputs=output_layer)
autoencoder.compile(optimizer='adam',loss='mse')
return autoencoder
autoencoder = build_autoencoder(input_dim=2)
autoencoder.fit(normal_data,normal_data,epochs=20)
reconstructed=autoencoder.predict(data)
reconstructed_error=np.mean((data-reconstructed)**2,axis=1)
reconstructed_error
predicted_labels=(reconstructed_error>40)
plt.scatter(data[:,0],data[:,1],
c=predicted_labels,cmap='coolwarm',alpha=0.6)
#plt.colorbar(label='Anomaly')

OUTPUT












Practical - 07
Aim: Using advanced optimization techniques like evolutionary algorithms or Bayesian optimization for hyperparameter tuning.
! pip install pygad
import numpy as np
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.svm import SVC
from sklearn.metrics import accuracy_score
import pygad
data = load_iris()
X,y = data.data,data.target
xtrain,xtest,ytrain,ytest = train_test_split(X,y,test_size=0.2,random_state=42)
def fitness_func(ga_instance,solution,solution_idx):
C=solution[0]
gamma=solution[1]
kernel = ['linear', 'poly','rbf','sigmoid'][int(solution[2])]
if kernel not in ['linear','poly','rbf','sigmoid']:
return 0
model= SVC(C=C,gamma=gamma,kernel=kernel, random_state=42)
model.fit(xtrain,ytrain)
predictions=model.predict(xtest)
accuracy = accuracy_score(ytest,predictions)
return accuracy
ga_instance = pygad.GA(num_generations=50,num_parents_mating=5,fitness_func=fitness_func,sol_per_pop=10,num_genes=3,gene_space=[
{ 'low': 0.1, 'high': 10.0},
{ 'low': 0.0001, 'high': 1.0},
{ 'low': 0, 'high': 3, 'step': 1},
parent_selection_type="rank", keep_parents=2, crossover_type="single_point",
mutation_type="random",
mutation_percent_genes=10 )
ga_instance.run()
solution,solution_fitness,solution_idx = ga_instance.best_solution()
print (solution,solution_fitness,solution_idx)
C_best,gamma_best,kernel_best = solution
kernel_best = ['linear', 'poly','rbf','sigmoid'][int(kernel_best)]
kernel_best
best_model = SVC(C=C_best,gamma=gamma_best,kernel=kernel_best, random_state=42)
best_model.fit(xtrain,ytrain)
final_accuracy = accuracy_score(ytest,best_model.predict(xtest))
final_accuracy
OUTPUT
 
Practical – 08
Aim- Experiment with neural networks like GANs (Generative Adversarial Networks) using Python libraries like TensorFlow or PyTorch to generate new images based on a dataset of images
import torch
import torch.nn as nn
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms
import matplotlib.pyplot as plt
from torch.utils.data import DataLoader
# Generator Model
class Generator(nn.Module):
    def __init__(self, z_dim=100):
        super().__init__()
        self.model = nn.Sequential(
            nn.Linear(z_dim, 256), nn.LeakyReLU(0.2),
            nn.Linear(256, 512), nn.LeakyReLU(0.2),
            nn.Linear(512, 1024), nn.LeakyReLU(0.2),
            nn.Linear(1024, 28*28), nn.Tanh()
        def forward(self, z):
        return self.model(z).view(z.size(0), 1, 28, 28)
# Discriminator Model
class Discriminator(nn.Module):
  def __init__(self):
        super().__init__()
        self.model = nn.Sequential(
            nn.Flatten(),
            nn.Linear(28*28, 1024), nn.LeakyReLU(0.2),
            nn.Linear(1024, 512), nn.LeakyReLU(0.2),
            nn.Linear(512, 256), nn.LeakyReLU(0.2),
            nn.Linear(256, 1), nn.Sigmoid()
           def forward(self, x):
        return self.model(x)
# Data loading
transform = transforms.Compose([
    transforms.ToTensor(), transforms.Normalize([0.5], [0.5])
])
train_loader = DataLoader(
    torchvision.datasets.MNIST(root='./data', train=True, download=True, transform=transform),
    batch_size=64, shuffle=True
)
# Model, optimizer and loss function
z_dim = 100
generator, discriminator = Generator(z_dim), Discriminator()
criterion = nn.BCELoss()
optimizer_gen = optim.Adam(generator.parameters(), lr=0.0002, betas=(0.5, 0.999))
optimizer_disc = optim.Adam(discriminator.parameters(), lr=0.0002, betas=(0.5, 0.999))
# Training loop
for epoch in range(20):
    for real_images, _ in train_loader:
        batch_size = real_images.size(0)
        real_images = real_images.view(batch_size, -1)
         # Labels
        real_labels, fake_labels = torch.ones(batch_size, 1), torch.zeros(batch_size, 1)

       # Train Discriminator
        optimizer_disc.zero_grad()
        d_loss_real = criterion(discriminator(real_images), real_labels)
        d_loss_fake = criterion(discriminator(generator(torch.randn(batch_size, z_dim)).view(batch_size, -1)), fake_labels)
        d_loss = d_loss_real + d_loss_fake
        d_loss.backward()
        optimizer_disc.step()

        # Train Generator
        optimizer_gen.zero_grad()
        g_loss = criterion(discriminator(generator(torch.randn(batch_size, z_dim)).view(batch_size, -1)), real_labels)
        g_loss.backward()
        optimizer_gen.step()

    # Print loss and save images
    if (epoch+1) % 5 == 0:
        print(f"Epoch [{epoch+1}/20], D Loss: {d_loss.item():.4f}, G Loss: {g_loss.item():.4f}")
        with torch.no_grad():
            fake_images = generator(torch.randn(64, z_dim)).view(64, 1, 28, 28)
            fake_images = (fake_images + 1) / 2  # Rescale to [0, 1]
            grid_img = torchvision.utils.make_grid(fake_images)
            plt.imshow(grid_img.permute(1, 2, 0).cpu().numpy())
            plt.show()

Output
 



