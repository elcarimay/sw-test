```python
##신규 tensorflow 2.0버전
import tensorflow as tf

# 모델 파라미터
W = tf.Variable([.3], dtype=tf.float32)
b = tf.Variable([-.3], dtype=tf.float32)

# 모델 입력과 출력
@tf.function
def linear_model(x):
    return W * x + b

# 손실 함수
@tf.function
def loss(y, y_pred):
    return tf.reduce_sum(tf.square(y_pred - y))

# 옵티마이저
optimizer = tf.keras.optimizers.SGD(learning_rate=0.01)

# 훈련 데이터
x_train = tf.constant([1, 2, 3, 4], dtype=tf.float32)
y_train = tf.constant([0, -1, -2, -3], dtype=tf.float32)

# 훈련 루프
for i in range(1000):
    with tf.GradientTape() as tape:
        y_pred = linear_model(x_train)
        curr_loss = loss(y_train, y_pred)
    gradients = tape.gradient(curr_loss, [W, b])
    optimizer.apply_gradients(zip(gradients, [W, b]))

# 훈련 정확도 평가
y_pred = linear_model(x_train)
curr_loss = loss(y_train, y_pred)
print("W: %s b: %s loss: %s" % (W.numpy(), b.numpy(), curr_loss.numpy()))
```
```python
import tensorflow as tf
import numpy as np
import random
from tensorflow.keras.datasets import mnist
import matplotlib.pyplot as plt

# MNIST 데이터 로드
with np.load('mnist.npz') as f:
    x_train, y_train = f['x_train'], f['y_train']
    x_test, y_test = f['x_test'], f['y_test']
# (x_train, y_train), (x_test, y_test) = mnist.load_data()

# 데이터 형태 변경
x_train = x_train.reshape(-1, 784) / 255.0
x_test = x_test.reshape(-1, 784) / 255.0

# 원-핫 인코딩 적용
y_train = tf.keras.utils.to_categorical(y_train)
y_test = tf.keras.utils.to_categorical(y_test)

# 모델 정의
model = tf.keras.Sequential([
    tf.keras.Input(shape=(784,)),
    tf.keras.layers.Dense(10, activation='softmax')
])

# model = tf.keras.models.Sequential([
#     tf.keras.layers.Dense(10, activation='softmax', input_shape=(784,))
# ])

# 모델 컴파일
model.compile(optimizer='sgd', loss='categorical_crossentropy', metrics=['accuracy'])

# 모델 학습
model.fit(x_train, y_train, epochs=15, batch_size=100, verbose=2)

# 학습 결과 테스트
loss, accuracy = model.evaluate(x_test, y_test)
print("Accuracy: ", accuracy)

# 랜덤한 이미지 선택
r = random.randint(0, len(x_test) - 1)
print("Label: ", np.argmax(y_test[r:r + 1], axis=1))
print("Prediction: ", np.argmax(model.predict(x_test[r:r + 1]), axis=1))

plt.imshow(x_test[r:r + 1].reshape(28, 28), cmap='Greys', interpolation='nearest')
plt.show()
```
```python
# 신규 2.0
import numpy as np
import tensorflow as tf

# MNIST 데이터 로드
with np.load('mnist.npz') as f:
    x_train, y_train = f['x_train'], f['y_train']
    x_test, y_test = f['x_test'], f['y_test']

# 데이터 형태 변경
x_train = x_train.reshape(-1, 784) / 255.0 # 28*28
x_test = x_test.reshape(-1, 784) / 255.0

# 원-핫 인코딩 적용
y_train = tf.keras.utils.to_categorical(y_train)
y_test = tf.keras.utils.to_categorical(y_test)

# 모델 정의
x = tf.keras.Input(shape=(784,))
W = tf.keras.layers.Dense(10, kernel_initializer='zeros', bias_initializer='zeros')
y = tf.keras.layers.Softmax()(W(x))

# 모델 컴파일
model = tf.keras.Model(inputs=x, outputs=y)
model.compile(optimizer=tf.keras.optimizers.SGD(0.5), loss='categorical_crossentropy', metrics=['accuracy'])

# 모델 학습
model.fit(x_train, y_train, epochs=15, batch_size=100, verbose=2) # 100개의 이미지씩 15번 반복 test를 진행한다는 의미

# 모델 평가
loss, accuracy = model.evaluate(x_test, y_test)
print('Accuracy:', accuracy) # true 1, false 0으로 하고 평균을 구함
```
```python
import numpy as np
import tensorflow as tf
from tensorflow.keras.datasets import mnist

# MNIST 데이터 로드
with np.load('mnist.npz') as f:
    x_train, y_train = f['x_train'], f['y_train']
    x_test, y_test = f['x_test'], f['y_test']

# 데이터 형태 변경
x_train = x_train.reshape(-1, 28, 28, 1) / 255.0
x_test = x_test.reshape(-1, 28, 28, 1) / 255.0

# 원-핫 인코딩 적용
y_train = tf.keras.utils.to_categorical(y_train)
y_test = tf.keras.utils.to_categorical(y_test)

# 입력 데이터 정의
inputs = tf.keras.Input(shape=(28, 28, 1))

# 모델 정의
x = tf.keras.layers.Conv2D(32, (3, 3), padding = 'same', activation='relu')(inputs)
x = tf.keras.layers.MaxPooling2D((2, 2), strides = 2)(x)
x = tf.keras.layers.Conv2D(64, (3, 3), padding = 'same', activation='relu')(x)
x = tf.keras.layers.MaxPooling2D((2, 2), strides = 2)(x)
x = tf.keras.layers.Flatten()(x)
x = tf.keras.layers.Dense(256, activation='relu')(x)
outputs = tf.keras.layers.Dense(10, activation='softmax')(x)

# 모델 정의
model = tf.keras.Model(inputs=inputs, outputs=outputs)

# 모델 컴파일
model.compile(optimizer=tf.keras.optimizers.Adam(0.001), loss='categorical_crossentropy', metrics=['accuracy'])

# 모델 학습
model.fit(x_train, y_train, epochs=10, batch_size=100, verbose=2)

# 모델 평가
loss, accuracy = model.evaluate(x_test, y_test)
print('Accuracy:', accuracy)
```
```python
import numpy as np
import tensorflow as tf
from tensorflow.keras.datasets import mnist

# MNIST 데이터 로드
with np.load('mnist.npz') as f:
    x_train, y_train = f['x_train'], f['y_train']
    x_test, y_test = f['x_test'], f['y_test']

# 데이터 형태 변경
x_train = x_train.reshape(-1, 28, 28, 1) / 255.0
x_test = x_test.reshape(-1, 28, 28, 1) / 255.0

# 원-핫 인코딩 적용
y_train = tf.keras.utils.to_categorical(y_train)
y_test = tf.keras.utils.to_categorical(y_test)

# 입력 데이터 정의
inputs = tf.keras.Input(shape=(28, 28, 1))

# 모델 정의
x = tf.keras.layers.Conv2D(32, (3, 3), padding = 'same', activation='relu')(inputs)
x = tf.keras.layers.MaxPooling2D((2, 2), strides = 2)(x)
x = tf.keras.layers.Conv2D(64, (3, 3), padding = 'same', activation='relu')(x)
x = tf.keras.layers.MaxPooling2D((2, 2), strides = 2)(x)
x = tf.keras.layers.Flatten()(x)
x = tf.keras.layers.Dense(256, activation='relu')(x)
x = tf.keras.layers.Dropout(0.7)(x)
outputs = tf.keras.layers.Dense(10, activation='softmax')(x)

# 모델 정의
model = tf.keras.Model(inputs=inputs, outputs=outputs)

# 모델 컴파일
model.compile(optimizer=tf.keras.optimizers.Adam(0.001), loss='categorical_crossentropy', metrics=['accuracy'])

# 모델 학습
model.fit(x_train, y_train, epochs=10, batch_size=100, verbose=2)

# 모델 평가
loss, accuracy = model.evaluate(x_test, y_test)
print('Accuracy:', accuracy)
```
```python
import tensorflow as tf
import numpy as np

# 문자와 숫자 매핑
char_arr = list('abcdefghijklmnopqrstuvwxyz')
num_dic = {n: i for i, n in enumerate(char_arr)}
dic_len = len(num_dic)

# 데이터셋
seq_data = ['word', 'wood', 'deep', 'dive', 'cold',
            'cool', 'load', 'love', 'kiss', 'kind']

# 배치 생성 함수
def make_batch(seq_data):
    input_batch, target_batch = [], []
    
    for seq in seq_data:
        input = [num_dic[n] for n in seq[:-1]]
        target = num_dic[seq[-1]]
        input_batch.append(tf.one_hot(input, dic_len))
        target_batch.append(target)
    
    return np.array(input_batch), np.array(target_batch)

# 하이퍼파라미터
learning_rate = 0.01
n_hidden = 128
total_epoch = 50
n_step = 3
n_class = dic_len

# 입력 데이터 생성
input_batch, target_batch = make_batch(seq_data)

# 모델 정의
class CharLSTM(tf.keras.Model):
    def __init__(self, n_hidden, n_class):
        super(CharLSTM, self).__init__()
        self.lstm1 = tf.keras.layers.LSTM(n_hidden, return_sequences=True, dropout=0.5)
        self.lstm2 = tf.keras.layers.LSTM(n_hidden)
        self.fc = tf.keras.layers.Dense(n_class)

    def call(self, x, training=False):
        x = self.lstm1(x, training=training)
        x = self.lstm2(x, training=training)
        return self.fc(x)

model = CharLSTM(n_hidden, n_class)

# 손실 함수와 최적화
loss_fn = tf.keras.losses.SparseCategoricalCrossentropy(from_logits=True)
optimizer = tf.keras.optimizers.Adam(learning_rate)

# 정확도 측정 함수
train_acc = tf.keras.metrics.SparseCategoricalAccuracy()

# 학습 루프
for epoch in range(total_epoch):
    with tf.GradientTape() as tape:
        logits = model(input_batch, training=True)
        loss = loss_fn(target_batch, logits)
    grads = tape.gradient(loss, model.trainable_variables)
    optimizer.apply_gradients(zip(grads, model.trainable_variables))
    
    train_acc.update_state(target_batch, logits)
    print(f'Epoch {epoch+1:04d}, Loss: {loss:.6f}, Accuracy: {train_acc.result().numpy():.4f}')
    train_acc.reset_state()

# 예측
logits = model(input_batch, training=False)
preds = tf.argmax(logits, axis=1).numpy()

# 결과 출력
predict_words = []
for idx, val in enumerate(seq_data):
    last_char = char_arr[preds[idx]]
    predict_words.append(val[:3] + last_char)

print('Inserted:', [w[:3] + ' ' for w in seq_data])
print('Predicted:', predict_words)

accuracy_val = np.mean(np.array(preds) == target_batch)
print('Accuracy:', accuracy_val)

```
```python
import tensorflow as tf
import numpy as np

# 문자 집합과 딕셔너리
char_arr = [c for c in 'SEabcdefghijklmnopqrstuvwxyz단어나무놀이사과범컴퓨터']
num_dic = {n: i for i, n in enumerate(char_arr)}
dic_len = len(num_dic)

seq_data = [['word', '단어'], ['wood', '나무'],
            ['gate', '놀이'], ['apple', '사과'],
            ['tiger', '범'], ['computer', '컴퓨터']]

max_length = 10

# 배치 생성 함수
def make_batch(seq_data):
    batch_e, batch_d, batch_y, len_y = [], [], [], []

    for seq in seq_data:
        # Encoder Input
        input = np.zeros((max_length, dic_len))
        for i, n in enumerate(seq[0]):
            input[i, num_dic[n]] = 1

        # Decoder Input ('S' + target)
        output = np.zeros((max_length, dic_len))
        for i, n in enumerate('S' + seq[1]):
            output[i, num_dic[n]] = 1

        # Decoder Target (target + 'E')
        target = np.zeros(max_length, dtype=np.int64)
        for i, n in enumerate(seq[1] + 'E'):
            target[i] = num_dic[n]

        batch_e.append(input)
        batch_d.append(output)
        batch_y.append(target)
        len_y.append(len(seq[1]) + 1)

    return np.array(batch_e), np.array(batch_d), np.array(batch_y), np.array(len_y)


# 하이퍼파라미터
learning_rate = 0.01
n_hidden = 128
n_class = dic_len
total_epoch = 100

# 배치 준비
batch_e, batch_d, batch_y, len_y = make_batch(seq_data)

# 모델 정의
class Seq2Seq(tf.keras.Model):
    def __init__(self, n_hidden, n_class, max_length):
        super(Seq2Seq, self).__init__()
        self.encoder_rnn = tf.keras.layers.SimpleRNN(n_hidden, return_state=True, return_sequences=True)
        self.decoder_rnn = tf.keras.layers.SimpleRNN(n_hidden, return_sequences=True, return_state=True)
        self.dense = tf.keras.layers.Dense(n_class)
        self.max_length = max_length

    def call(self, enc_input, dec_input, training=False):
        enc_output, enc_state = self.encoder_rnn(enc_input, training=training)
        dec_output, _ = self.decoder_rnn(dec_input, initial_state=enc_state, training=training)
        output = self.dense(dec_output)
        return output

model = Seq2Seq(n_hidden, n_class, max_length)
loss_fn = tf.keras.losses.SparseCategoricalCrossentropy(from_logits=True, reduction='none')
optimizer = tf.keras.optimizers.Adam(learning_rate)

# 학습 루프
for epoch in range(total_epoch):
    with tf.GradientTape() as tape:
        logits = model(batch_e, batch_d, training=True)

        # Mask 적용
        mask = tf.sequence_mask(len_y, maxlen=max_length, dtype=tf.float32)
        loss = loss_fn(batch_y, logits)
        loss = tf.reduce_sum(loss * mask) / tf.reduce_sum(mask)

    grads = tape.gradient(loss, model.trainable_variables)
    optimizer.apply_gradients(zip(grads, model.trainable_variables))

    print(f'Epoch {epoch+1:04d}, Loss: {loss:.6f}')


# 번역 함수 (Inference)
def translate(word):
    seq_data = [[word, '']]
    batch_e, _, _, _ = make_batch(seq_data)

    enc_output, enc_state = model.encoder_rnn(batch_e, training=False)

    dec_input = np.zeros((1, 1, dic_len), dtype=np.float32)
    dec_input[0, 0, num_dic['S']] = 1

    result = ''
    dec_state = enc_state

    for _ in range(max_length):
        dec_output, dec_state = model.decoder_rnn(dec_input, initial_state=dec_state, training=False)
        logits = model.dense(dec_output)
        pred_id = tf.argmax(logits[0, 0]).numpy()
        char = char_arr[pred_id]
        if char == 'E':
            break
        result += char

        # 다음 입력
        dec_input = np.zeros((1, 1, dic_len), dtype=np.float32)
        dec_input[0, 0, pred_id] = 1

    return result


# 테스트
for word in ('word', 'wood', 'gate', 'apple', 'tiger', 'computing'):
    print(word, '->', translate(word))

print()

for word in ('weed', 'wolf', 'woman', 'qweqwe'):
    print(word, '->', translate(word))
```
```python
import matplotlib.pyplot as plt
import tensorflow as tf
from tensorflow.keras.datasets import mnist

# Hyperparameters
learning_rate = 0.01
training_epoch = 20
batch_size = 100
n_hidden = 256
n_input = 28*28

# Load MNIST dataset
with np.load('mnist.npz') as f:
    X_train, _ = f['x_train'], f['y_train']
    X_test, _ = f['x_test'], f['y_test']

# Reshape and normalize data
X_train = X_train.reshape(-1, n_input) / 255.0
X_test = X_test.reshape(-1, n_input) / 255.0

# Define autoencoder model
model = tf.keras.models.Sequential([
    tf.keras.layers.Dense(n_hidden, activation='sigmoid', input_shape=(n_input,)),
    tf.keras.layers.Dense(n_input, activation='sigmoid')
])

# Compile model
model.compile(optimizer=tf.keras.optimizers.Adam(learning_rate), loss='mean_squared_error')

# Train model
model.fit(X_train, X_train, epochs=training_epoch, batch_size=batch_size)

# Test model
n = 4
canvas_orig = np.empty((28*n, 28*n))
canvas_recon = np.empty((28*n, 28*n))
for i in range(n):
    batch_x = X_test[i*n:(i+1)*n]
    g = model.predict(batch_x)
    for j in range(n):
        canvas_orig[i*28:(i+1)*28, j*28:(j+1)*28] = batch_x[j].reshape([28,28])
    for j in range(n):
        canvas_recon[i*28:(i+1)*28, j*28:(j+1)*28] = g[j].reshape([28,28])

# Plot results
print("Original Images")
plt.figure(figsize=(n,n))
plt.imshow(canvas_orig, origin="upper", cmap="gray")
plt.show()
print("Reconstructed Images")
plt.figure(figsize=(n,n))
plt.imshow(canvas_recon, origin="upper", cmap="gray")
plt.show()
```
```python
import tensorflow as tf
from tensorflow.keras.datasets import mnist
import matplotlib.pyplot as plt

# Hyperparameters
training_epoch = 20
batch_size = 100

# Load MNIST dataset
with np.load('mnist.npz') as f:
    X_train, _ = f['x_train'], f['y_train']
    X_test, _ = f['x_test'], f['y_test']

# Reshape and normalize data
X_train = X_train.reshape(-1, 28, 28, 1) / 255.0
X_test = X_test.reshape(-1, 28, 28, 1) / 255.0

# Define autoencoder model
model = tf.keras.models.Sequential([
    tf.keras.layers.Conv2D(32, (5, 5), strides=(2, 2), activation='relu', input_shape=(28, 28, 1)),
    tf.keras.layers.Flatten(),
    tf.keras.layers.Dense(256, activation='relu'),
    tf.keras.layers.Dense(14*14*32, activation='relu'),
    tf.keras.layers.Reshape((14, 14, 32)),
    tf.keras.layers.Conv2DTranspose(1, (5, 5), strides=(2, 2), activation='sigmoid', padding='same')
])

# Compile model
model.compile(optimizer='adam', loss='mean_squared_error')

# Train model
model.fit(X_train, X_train, epochs=training_epoch, batch_size=batch_size)

# Test model
n = 4
canvas_orig = np.empty((28*n, 28*n))
canvas_recon = np.empty((28*n, 28*n))
for i in range(n):
    batch_x = X_test[i*n:(i+1)*n]
    g = model.predict(batch_x)
    for j in range(n):
        canvas_orig[i*28:(i+1)*28, j*28:(j+1)*28] = batch_x[j].reshape([28,28])
    for j in range(n):
        canvas_recon[i*28:(i+1)*28, j*28:(j+1)*28] = g[j].reshape([28,28])

# Plot results
print("Original Images")
plt.figure(figsize=(n,n))
plt.imshow(canvas_orig, origin="upper", cmap="gray")
plt.show()
print("Reconstructed Images")
plt.figure(figsize=(n,n))
plt.imshow(canvas_recon, origin="upper", cmap="gray")
plt.show()
```
```python
import tensorflow as tf
from tensorflow.keras.datasets import mnist
import numpy as np
import matplotlib.pyplot as plt

# Hyperparameters
total_epoch = 100
batch_size = 100
learning_rate = 0.0002
n_hidden = 256
n_input = 28*28
n_noise = 128

# Load MNIST dataset
with np.load('mnist.npz') as f:
    X_train, _ = f['x_train'], f['y_train']
    _, _ = f['x_test'], f['y_test']

X_train = X_train.reshape(-1, n_input) / 255.0

# Define generator model
generator = tf.keras.models.Sequential([
    tf.keras.layers.Dense(n_hidden, activation='relu', input_shape=(n_noise,)),
    tf.keras.layers.Dense(n_input, activation='sigmoid')
])

# Define discriminator model
discriminator = tf.keras.models.Sequential([
    tf.keras.layers.Dense(n_hidden, activation='relu', input_shape=(n_input,)),
    tf.keras.layers.Dense(1, activation='sigmoid')
])

# Define loss functions and optimizers
def generator_loss(y_pred):
    return -tf.reduce_mean(tf.math.log(y_pred))

def discriminator_loss(y_real, y_fake):
    return -tf.reduce_mean(tf.math.log(y_real) + tf.math.log(1 - y_fake))

generator_optimizer = tf.keras.optimizers.Adam(learning_rate)
discriminator_optimizer = tf.keras.optimizers.Adam(learning_rate)

# Train models
for epoch in range(total_epoch):
    for i in range(len(X_train) // batch_size):
        batch_xs = X_train[i*batch_size:(i+1)*batch_size]
        noise = np.random.normal(size=(batch_size, n_noise))
        
        with tf.GradientTape() as gen_tape, tf.GradientTape() as disc_tape:
            gen_output = generator(noise, training=True)
            real_output = discriminator(batch_xs, training=True)
            fake_output = discriminator(gen_output, training=True)
            
            gen_loss = generator_loss(fake_output)
            disc_loss = discriminator_loss(real_output, fake_output)
            
        gradients_of_generator = gen_tape.gradient(gen_loss, generator.trainable_variables)
        gradients_of_discriminator = disc_tape.gradient(disc_loss, discriminator.trainable_variables)
        
        generator_optimizer.apply_gradients(zip(gradients_of_generator, generator.trainable_variables))
        discriminator_optimizer.apply_gradients(zip(gradients_of_discriminator, discriminator.trainable_variables))
        
    print('Epoch: ', '%04d' % epoch, 'D loss: {:.4}'.format(disc_loss), 'G loss: {:.4}'.format(gen_loss))
    
    if epoch == 0 or (epoch + 1) % 10 == 0:
        sample_size = 10
        noise = np.random.normal(size=(sample_size, n_noise))
        samples = generator(noise, training=False)
        
        fig, ax = plt.subplots(1, sample_size, figsize=(sample_size, 1))
        for i in range(sample_size):
            ax[i].set_axis_off()
            ax[i].imshow(np.reshape(samples[i], (28,28)))
        plt.show()
        plt.close(fig)
```
```python
import tensorflow as tf
from tensorflow import keras
from tensorflow.keras import layers
import numpy as np
import matplotlib.pyplot as plt
from tensorflow.keras.datasets import mnist

# 하이퍼파라미터 설정
total_epoch = 100
batch_size = 100
learning_rate = 0.0002
n_hidden = 256
n_input = 28*28
n_noise = 128
n_label = 10

# 데이터 불러오기
(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train = x_train.reshape((-1, n_input)) / 255.0
y_train = keras.utils.to_categorical(y_train, n_label)

# 모델 정의
def generator():
    model = keras.Sequential([
        layers.Dense(n_hidden, activation='relu', input_shape=(n_noise + n_label,)),
        layers.Dense(n_input, activation='sigmoid')
    ])
    return model

def discriminator():
    model = keras.Sequential([
        layers.Dense(n_hidden, activation='relu', input_shape=(n_input + n_label,)),
        layers.Dense(1)
    ])
    return model

# 모델 생성
G = generator()
D = discriminator()

# 손실 함수 정의
def loss_D(D, real, fake):
    loss_D_real = tf.reduce_mean(tf.nn.sigmoid_cross_entropy_with_logits(
        logits=real, labels=tf.ones_like(real)))
    loss_D_fake = tf.reduce_mean(tf.nn.sigmoid_cross_entropy_with_logits(
        logits=fake, labels=tf.zeros_like(fake)))
    return loss_D_real + loss_D_fake

def loss_G(D, fake):
    return tf.reduce_mean(tf.nn.sigmoid_cross_entropy_with_logits(
        logits=fake, labels=tf.ones_like(fake)))

# 옵티마이저 정의
D_optimizer = keras.optimizers.Adam(learning_rate)
G_optimizer = keras.optimizers.Adam(learning_rate)

# 학습
for epoch in range(total_epoch):
    for i in range(len(x_train) // batch_size):
        batch_xs = x_train[i*batch_size:(i+1)*batch_size]
        batch_ys = y_train[i*batch_size:(i+1)*batch_size]
        noise = np.random.normal(size=(batch_size, n_noise))
        
        with tf.GradientTape() as gen_tape, tf.GradientTape() as disc_tape:
            inputs = tf.concat([batch_xs, batch_ys], axis=1)
            real = D(inputs, training=True)
            
            inputs = tf.concat([noise, batch_ys], axis=1)
            fake = G(inputs, training=True)
            inputs = tf.concat([fake, batch_ys], axis=1)
            fake = D(inputs, training=True)
            
            loss_D_val = loss_D(D, real, fake)
            loss_G_val = loss_G(D, fake)
        
        gradients_of_D = disc_tape.gradient(loss_D_val, D.trainable_variables)
        D_optimizer.apply_gradients(zip(gradients_of_D, D.trainable_variables))
        
        gradients_of_G = gen_tape.gradient(loss_G_val, G.trainable_variables)
        G_optimizer.apply_gradients(zip(gradients_of_G, G.trainable_variables))
    
    print('Epoch: ', '%04d' % epoch,
          'D loss: {:.4}'.format(loss_D_val),
          'G loss: {:.4}'.format(loss_G_val))
    
    if epoch % 10 == 0:
        sample_size = 3
        noise = np.random.normal(size=(sample_size*n_label, n_noise))
        y_sample = np.eye(n_label)
        for i in range(1, sample_size):
            y_sample = np.concatenate([y_sample, np.eye(n_label)])
        inputs = tf.concat([noise, y_sample], axis=1)
        samples = G(inputs, training=False)
        samples = samples.numpy()
        
        fig, ax = plt.subplots(sample_size, n_label, figsize=(n_label, sample_size))
        for i in range(sample_size):
            for j in range(n_label):
                ax[i][j].set_axis_off()
                ax[i][j].imshow(np.reshape(samples[i*n_label+j], (28,28)))
        plt.savefig('samples/{}.png'.format(str(epoch).zfill(3)))
        plt.show()
        plt.close(fig)
```
