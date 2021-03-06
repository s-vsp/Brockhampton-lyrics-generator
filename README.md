# Brockhampton lyrics generator
LSTM RNN approach to generate new Brockhampton songs.

*Keywords: Deep Learning, RNN, LSTM, One-Step Forecasting, TensorFlow, Keras, Text Generation, Music Generation*

# Table of contents:
- [Idea](#Idea)
- [Project](#Project)
  - [Preprocessing](#Preprocessing)
  - [Training](#Training)
  - [Evaluating](#Evaluating)
- [Further tasks](#Further_tasks)
- [References](#References)


## Idea <a name="Idea"></a>

The main idea of the project was to generate lyrics using Long short-term memory recurrent neural network. Followed the TensorFlow tutorial [[1]](https://www.tensorflow.org/text/tutorials/text_generation) where Shakespeare's poems where generated based on GRU architecture I decided to perform a similar approach, but based on LSTM. LSTMs are genuinely described in Colah's blog [[2]](https://colah.github.io/posts/2015-08-Understanding-LSTMs/).
The dataset includes lyrics from all the Brockhampton songs gathered from [genius.com](https://genius.com/) and having 207 651 chars.

## Project <a name="Project"></a>

The whole project can be divided into 3 parts: preprocessing, training and evaluating. The full code is available in **bh_lstm.py** file.

### Preprocessing <a name="Preprocessing"></a>

During preprocessing the vocabulary from the loaded Brockhampton lyrics was created and included exactly 100 unique chars. Following that, 2 StringLookup mappings were generated - first one mapping chars into integers (char2int) and the second one mapping integers into chars (int2char). Lastly the data was transformed into TensorFlow dataset (from tensor slices) and proceeded as sequences made of 50 chars. Finally the data used for training the model was inputed in form of pairs -> (input [X], target [y]), both having the same shape, but formed the way that the input is the current char and target is the next char, e.g. X = [Hell], y = [ello]. Data was shuffled, controlled by the BUFFER_SIZE parameter and inputed to the net as batches of size 64.

### Training <a name="Training"></a>

Before training the model, the obvious thing to do was to build the whole network. It contains 3 layers: embedding layer, lstm layer and dense layer. During the first phases of this project dropout layer was used as well to prevent the model from overfitting. Nevertheless it yield to poor overall performance as overfitting was not a problem in this study. Here we can see model's summary:

<img src="https://i.imgur.com/o561Uxn.png" alt="Model's summary" width="600"/>

Training took place over 250 epochs and its performance was monitored using TensorBoard (loss and accuracy). Final accuracy was equal to 0.9543 and loss equal to 0.1280 (250th epoch of learning).\
\
Epoch 250/250\
81/81 [==============================] - 41s 510ms/step - loss: 0.1280 - accuracy: 0.9543\
\
Received scalars and the training process of this experiment are available on my [tensorboard.dev](https://tensorboard.dev/experiment/IVSeKcZgTgexe66frmiVsA/#scalars) account. Here is a shot of the final results:

<img src="https://i.imgur.com/bmklbgf.png" alt="Scalars" width="600"/>

### Evaluating <a name="Evaluating"></a>

Evaluating phase had two inside features. The first one was a function get_title() which simply generates titles (of the album and the songs). The second feature was a class OneStepForecasting(), which predicted chars at the next time step. Important thing that has to be mentioned on this point is a temperature hyperparameter. In simple words, modifying it changes model's confidence level in predicting the values -> smaller the temperature is, more confident the prediction is (and vice versa). To expand your knowledge about it, you can follow this short Medium article [[3]](https://medium.com/@majid.ghafouri/why-should-we-use-temperature-in-softmax-3709f4e0161) and Wikipedia article about a wider concept which is a softmax activation function itself [[4]](https://en.wikipedia.org/wiki/Softmax_function). In this study I tried different values of temperature, resulting in both, enlarging and reducing model's confidence. Surely, looking for the optimal value of it can be performed as a part of hyperparameter tuning (like grid search). Decided to go with a value of 1.5 (note, that the default value of it is set to be 1.0). Additionaly to show temperature's power and importance, evaluation (lyrics generation) was carefully analyzed on four values - 0.1, 1.0, 1.5 and 5.0 - resulting in strongly visible differences. With the value of 0.1 model had some sort of 'overfitting' tendence and didn't take any risk at predictions (looping sequences from the dataset). On the other hand, the value of 5.0 yield to similar behaviour as untrained network and the predictions were just random chars. Default value of 1.0 worked pretty solid, although it still had some looping tendence. As mentioned earlier, the perfect value in this study was 1.5 - good and accurate predictions.

<img src="https://i.imgur.com/aBXMKo3.png" alt="Temperature tuning" width="800"/>

The final model, with one step forecasting has been saved in [ONE_STEP_FORECAST_MODEL](https://github.com/s-vsp/Brockhampton-lyrics-generator/tree/main/ONE_STEP_FORECAST_MODEL) file and is easily accessible with this example line of code: `loaded_model = tf.saved_model.load('ONE_STEP_FORECAST_MODEL')`. The last piece of code is responsible for generating the album folder with 10 songs generated by the model (and their titles), e.g. [SPIRIT](https://github.com/s-vsp/Brockhampton-lyrics-generator/tree/main/SPIRIT).

## Further tasks <a name="Further_tasks"></a>

In future there are several possible approaches and techniques I would like to try. Some of them are: GRU model, LSTM model with more layers, Transformers (e.g. BERT) model and also customized training technique (TensorFlow).

## References <a name="References"></a>
- [1]. [Generate Text with RNNs](https://www.tensorflow.org/text/tutorials/text_generation)
- [2]. [LSTMs by Colah](https://colah.github.io/posts/2015-08-Understanding-LSTMs/)
- [3]. [Temperature hyperparamater](https://medium.com/@majid.ghafouri/why-should-we-use-temperature-in-softmax-3709f4e0161)
- [4]. [Softmax](https://en.wikipedia.org/wiki/Softmax_function)
