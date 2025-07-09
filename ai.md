python```
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
```
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
```
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
```
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
```
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
