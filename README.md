# URDU-nextword-prediction
Develop an LSTM-based Urdu next word prediction model. Collect and preprocess Urdu text data, train the model, and deploy it as a web app or API. Open-source for community contributions and advancements in Urdu NLP.
This project aims to develop a next word prediction model for the Urdu language using Long Short-Term Memory (LSTM) neural networks. Next word prediction is a key task in natural language processing (NLP) that involves predicting the next word in a sequence of text given the previous words. In this project, we focus specifically on the Urdu language, leveraging the power of LSTM networks, which are well-suited for sequence prediction tasks due to their ability to capture long-term dependencies in data.

Key Features:

Dataset Collection: We collect a large corpus of Urdu text data from various sources such as books, articles, and websites to train our LSTM model. This dataset is preprocessed to remove noise and irrelevant information.

Data Preprocessing: The collected Urdu text data undergoes preprocessing steps such as tokenization, lowercasing, and removing punctuation marks to prepare it for training the LSTM model.

LSTM Model Architecture: We design and implement an LSTM-based neural network architecture for next word prediction. The model consists of an embedding layer, LSTM layers, and a dense output layer.

Training: The LSTM model is trained on the preprocessed Urdu text data using techniques such as mini-batch gradient descent and backpropagation through time (BPTT). We optimize the model parameters to minimize the loss function and improve prediction accuracy.

Evaluation: We evaluate the performance of the trained LSTM model using metrics such as perplexity and accuracy. We also conduct qualitative analysis by generating sample predictions and assessing their coherence and relevance.

Deployment: The trained LSTM model is deployed as a web application or API, allowing users to input partial Urdu sentences and receive predictions for the next word in real-time.

Documentation: Comprehensive documentation is provided, including instructions for dataset collection, data preprocessing, model training, evaluation, and deployment. Additionally, we describe the architecture and implementation details of the LSTM model.

Future Work: We discuss potential enhancements and extensions to the project, such as incorporating attention mechanisms, exploring alternative neural network architectures, and expanding the dataset for improved performance.
code for this 
!pip install tensorflow numpy sklearn
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences
from tensorflow.keras.utils import to_categorical
import numpy as np

# Load your Urdu text dataset
with open('/content/drive/MyDrive/urdutext/urdu_sentences_dataset.csv', 'r', encoding='utf-8') as file:
    urdu_text = file.read()

# Tokenize the text
tokenizer = Tokenizer(char_level=False)
tokenizer.fit_on_texts([urdu_text])
total_words = len(tokenizer.word_index) + 1

# Convert text to sequences of integers
input_sequences = []
for line in urdu_text.split('\n'):
    token_list = tokenizer.texts_to_sequences([line])[0]
    for i in range(1, len(token_list)):
        n_gram_sequence = token_list[:i+1]
        input_sequences.append(n_gram_sequence)

# Pad sequences
max_sequence_len = max([len(x) for x in input_sequences])
input_sequences = np.array(pad_sequences(input_sequences, maxlen=max_sequence_len, padding='pre'))

# Create predictors and label
predictors, label = input_sequences[:,:-1],input_sequences[:,-1]
label = to_categorical(label, num_classes=total_words)
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Embedding, LSTM, Dense, Dropout

model = Sequential([
    Embedding(total_words, 100, input_length=max_sequence_len-1),
    LSTM(150, return_sequences=True),
    Dropout(0.2),
    LSTM(100),
    Dense(total_words, activation='softmax')
])

# Compile the model
model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
history = model.fit(predictors, label, epochs=100, verbose=1)
