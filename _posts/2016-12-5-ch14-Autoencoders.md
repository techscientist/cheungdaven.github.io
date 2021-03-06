---
layout: post
comments: true
categories: DeepLearning
---

* content
{:toc}

## 14 Autoencoders 
决定放弃更新这本书的翻译，因为今天发现北大已经出来翻译版本！参见
[dlbook](https://github.com/exacity/deeplearningbook-chinese) 2016-12-08, 但是学习还在继续，我会继续发关于本书的学习总结！

<p> Autoencoder也是一种神经网络，它的目的是将input“复制”到output，从内部结构来看，它有一个隐含层\(h\)用来表示input的压缩编码。autoencoder由两个部分组成：一个编码函数 \(h=f(x)\) 以及一个用来解码的解码函数 \(r=g(h)\). 如果autoencoder能够完全的解码，即对所有的\(x\)，都有 \(g(f(x))=x\), 这样autoencoder是没有什么特殊的作用的。相反，我们并不需要完全的解码能力，大多数情况下，只允许他们完成近似的拷贝。 因为这个模型需要考虑哪些input是需要拷贝，只从input中拷贝有用的数据。    </p>
  ![1](https://zsjzig-sn3301.files.1drv.com/y3pr987XnnSfH_mmRqFfyrSqFFJQ8ylcyztUB6D9d_CghhLW_2CRBowGKZlvYn_KuzbRqmB1oNpSPIXt_AJSsdPzMNOVrGeGz444xgXCoYeByS9h945wF04aYZzCeBscfLaMhtY9qRsdCy0FEIpfLldLZlhE8_7bwd3sOcPGEANjdE/2016-12-05_214110.png?psid=1)
<p>现在的autoencoder的设计灵感是从以前的编码和解码（将\(p_{encoder}(h|x)\)映射到\(p_{decoder}(x|h)\)）的过程产生的。     </p>
<p>Autoencoder的概念在1987年就已经出现在了神经网络研究当中，传统的autoencoder主要用来降维和特征学习，近年来，autoencoder和隐含变量模型的理论联系将autoencoder带入了生产模型研究的前沿，在接下来的第20章中，autoencoder可以被看成feedforward网络的一种特例，甚至用相同的方法进行训练（即minibatch梯度下降，gradients使用back-propagation计算）。但和普通的feedforward网络不同，autoencoder可以使用recirculation（比较原始输入的激活函数和输出的激活函数）来进行训练，recirculation相对back-propagation来说，更加的合理，但是很是被使用在机器学习领域。  </p>

### 14.1 Undercomplete Autoencoders
<p>将输入复制到输出，听起来似乎什么用的都没有，但是通常情况下我们并不关注输入。相反，我们关注的是隐含层\(h\)，我们希望隐含层能够包含很多有用的属性。 </p>
<p>如果\(h\)曾的维数小于\(x\)，autoencoder就可以用来进行特征提取，我们将这种情况称为undercomplete。在进行undercomplete表征学习时迫使autoencoder去捕捉训练数据中的最精华的信息。  </p>
<p>简单地，学习过程可以表示为最小化如下的损失函数：  </p>
<p>$$L(x,g(f(x)))$$  </p>
<p>其中L为损失函数，用来衡量\(x\)和\(g(f(x))\)的差异，类似于平方差。  </p>
<p>当decoder是线性函数的时候，L就是平方差,autoencoder和PCA一样得跨越相同的子空间。当\(f\)和\(g\)都是非线性函数的时候，Autoencoder有比PCA更加强大的非线性生成能力。不幸的是，如果encoderh和decoder太过强大，以至于都不需要autoencoder进行特征提取就能恢复输入数据，这样的autoencoder就是失败的了。例如，编码成是一维的，但是编码函数可以将\(x^{(i)}\)表示为\(i\)。</p>

### 14.2 Regularized Autoencoders
<p>如果编码层和输入层的维度一样或者大于输入层的维度，这个就成了overcomplete。这种情况下，即使是线性的编码函数和解码函数都可以完成从输入复制到输出的过程，并且没办法学习到任何有用的信息。</p>
<p>理想情况下，任何架构的autoencoder都可以成功的训练，前提是按照数据分布的复杂程度选择合适的编码层维度以及编码和解码函数。Rgularized Autoencoder就提供了这样的能力，它不通过选择小维度的编码层和使用浅层的编码和解码过程来实现这样的功能，而是使用一个损失函数式的模型具备除了从输入拷贝数据到输出的能力之外的一些属性。这些属性包括，特征的稀疏性，小的导数，以及对噪音和数据丢的的的鲁棒性。一个Regularized的autoencoder可以是非线性的或者是overcomplete的，但却能从训练数据中学习到有用的信息。  </p>
<p>除了这里讨论的regularized autoencoder之外，任何的含有隐含变量和推理过程的生成模型都能够被看作为一种autoencoder。例如variational autoencoder以及generative stochastic networks，这些模型因为本身就是为了最大化训练数序的概率分布，所以他能够很好的学习数据的特征。  </p>

#### 14.2.1 Sparse Autoencoders
<p>稀疏autoencoder在训练的过程当中在\(h\)层中含有一个稀疏惩罚函数\(\Omega(h)\)，于是误差函数的形式如下  </p>
<p>$$L(x,g(f(x)))+\Omega(h)$$    </p>
<p>其\(g(h)\)是解码输出函数，\(h=f(x)\)为编码输出。  </p>
<p>Sparse Autoencoder的主要目的是为了分类而进行特征学习。它必须能够相应数据集的统计特征，这种方法进行训练可以在复制过程中将学习特征作为一个副产品。</p>
<p>我们可以将\(\Omega(h)\)作为一个简单的regularizer项，和其他的regularizer（如weight decay）不同的是，对于这个regularizer并没有相应的贝叶斯解释。如在5.6.1章中描述的那样，带有的weight decay和其他的正则惩罚项的训练可以看成为对参数的先验分布的正则惩罚的MAP。例如，最大化\(p(\theta|x)\),就等同于最大化\(\log p(x|\theta)+\log p(\theta)\)，其中\(\log p(\theta)\)就是参数的先验分布。而这里的惩罚函数不仅依赖输入数据，并且也不是任何形式的先验部分的定义。但是我们仍然可以将其看成是对某个函数的偏好的隐含表达方法。 </p>  
<p>现在我们不把这个惩罚项看作是regularizer，而是把它看作是有隐含变量的生成模型的近似的最大似然，假如我们的模型，拥有一些已知变量\(x\)以及一些隐含变量\(h\), 而\(p_{model}(h)\)是模型对隐含变量的先验概率，表示模型对\(x\)的信念度。这里的先验和我们之前的先验不同。对数似然为</p>
<p>$$\log p_{model}(x) = \log \sum_{h} p_{model}(h,x)$$</p>
<p>我们可以把autoencoder看作是这个带有一个h的很高可能值的点估计的和的近似。这和稀疏编码生成模型很相似，但是这里的\(h\)代表了编码的输出而不是对h的最有可的的值的优化。从这个观点来看，选择了这个\(h\)，我们要最大化</p>
<p>$$\log p_{model}(h,x) = \log p_{model}(h)+\log p_{model}(x|h) $$</p>
<p>这个 \(\log p_{model}(h)\) 项可以是sparsity-inducing。例如，Laplace先验</p>
<p>$$p_{model}(h_i) = \frac{\lambda}{2} e^-{\lambda|h_{i}|}$$</p>
<p>与绝对值稀疏惩罚对于，将log-prior表示成为绝对值惩罚，如下</p>
<p>$$\Omega(h)=\lambda \sum_{i}|h_{i}|$$</p>
<p>$$-\log p_{model}(h) = \sum_{i}(\lambda|h_{i}|-\log \frac{\lambda}{2}) = \Omega(h) + const$$</p>
<p> 这里的常数项取决于\(\lambda\)而不取决于\(h\), 我么将\(\lambda\)看成是超参数，并且去掉常数项，因为它并不影响参数的学习。其他的先验分布例如学生t分布也可以用来导致稀疏。从这个角度来看，这里的稀疏惩罚并不是正则项，只是模型对隐含变量的分布的结果。这个也给出了训练autoencoder的另外一层解释，这是对训练生成模型的近似。同时它也解释了为什么autoencoder的特征学习是有用的，因为他们描述了对输入的隐含变量。</p>

### 14.2.2 Denoising Autoencoders
<p>这里我们不再添加\(\Omega\)作为惩罚，我们通过重构损失函数得到另外一种Autoencoder，传统的情况下，我们是最小化如下函数</p>
<p>$$L(x,g(f(x)))$$  </p>
<p>其中L为损失函数，而\(L^2\)则代表norm，这个损失函数鼓励\(g, f\)为反函数，即输入和输出能够完全的解码。与似乎这里就有了denoising autoencoder，这里的损失函数变为：</p>

### 代码实现
{% highlight python %}
from __future__ import division, print_function, absolute_import

import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt

from tensorflow.examples.tutorials.mnist import input_data
mnist = input_data.read_data_sets("MNIST_DATA", one_hot=True)

# param
learning_rate = 0.01
training_epochs = 20
batch_size = 256
display_step = 1
examples_to_show = 10

# network param
n_hidden_1 = 256
n_hidden_2 = 128
n_input = 784

X = tf.placeholder("float",[None, n_input])

weights = {
    'encoder_h1':tf.Variable(tf.random_normal([n_input, n_hidden_1])),
    'encoder_h2':tf.Variable(tf.random_normal([n_hidden_1, n_hidden_2])),
    'decoder_h1':tf.Variable(tf.random_normal([n_hidden_2, n_hidden_1])),
    'decoder_h2':tf.Variable(tf.random_normal([n_hidden_1, n_input])),
}

biases = {
    'encoder_b1': tf.Variable(tf.random_normal([n_hidden_1])),
    'encoder_b2': tf.Variable(tf.random_normal([n_hidden_2])),
    'decoder_b1': tf.Variable(tf.random_normal([n_hidden_1])),
    'decoder_b2': tf.Variable(tf.random_normal([n_input])),
}

# building the decoder
def decoder(x):
    layer_1 = tf.nn.sigmoid(tf.add(tf.matmul(x, weights['decoder_h1']),biases['decoder_b1']))
    layer_2 = tf.nn.sigmoid(tf.add(tf.matmul(layer_1, weights['decoder_h2']), biases['decoder_b2']))
    return layer_2

# buildingthe encoder
def encoder(x):
    layer_1 = tf.nn.sigmoid(tf.add(tf.matmul(x, weights['encoder_h1']),biases['encoder_b1']))
    layer_2 = tf.nn.sigmoid(tf.add(tf.matmul(layer_1, weights['encoder_h2']), biases['encoder_b2']))
    return layer_2

# construct model
encoder_op = encoder(X)
decoder_op = decoder(encoder_op)

# Prediction
y_pred = decoder_op

# Targets are the input data
y_true = X

# define loss and optimizer, minimize the squared error
cost = tf.reduce_mean(tf.pow(y_true - y_pred, 2))
optimizer = tf.train.RMSPropOptimizer(learning_rate).minimize(cost)

# initializing the variables
init = tf.global_variables_initializer()

with tf.Session() as sess:
    sess.run(init)
    total_batch = int(mnist.train.num_examples/batch_size)

    for epoch in range(training_epochs):
        for i in range(total_batch):
            batch_xs, batch_ys = mnist.train.next_batch(batch_size)

            _, c = sess.run([optimizer, cost], feed_dict={X: batch_xs})

        if epoch % display_step == 0:
            print("Epoch:",'%04d' % (epoch+1), "cost=", "{:.9f}".format(c))

    print("Optimization finished!")

    # Applying encode and decode over the test set
    encode_decode = sess.run(
        y_pred, feed_dict={X: mnist.test.images[:examples_to_show]}
    )
    # Compare originla images with their reconstructions
    f, a = plt.subplots(2, 10, figsize =(10,2))

    for i in range(examples_to_show):
        a[0][i].imshow(np.reshape(mnist.test.images[i], (28,28)))
        a[1][i].imshow(np.reshape(encode_decode[i],(28,28)))
    f.show()
    plt.draw()
    plt.waitforbuttonpress()
{% endhighlight %} 
