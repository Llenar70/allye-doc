---
sidebar_position: 25
---

# Machine learning predictive model prototyping

> _Optimizing business processes through prediction is extremely powerful and can lead to dramatic gains in efficiency. The best way to build strong models is through rapid prototyping and iterative trial-and-error._

You can build predictive models by following a consistent framework. First, reshape the data so it is easy to analyze and split it into training and evaluation sets. Next, choose a learner suited to the objective, train and tune it, and validate its performance with cross-validation. Finally, run inference on test data to evaluate how well the model generalizes to unseen data.
By mastering and executing this flow quickly, you can produce reproducible, high-quality models for any prediction task.

With Allye you can complete this prototype in just five minutes—experience the incredible productivity for yourself!

Here we will use the classic iris dataset to build a classification model.

<p>
  <img
    src={require('./img/ml_prototyping.png').default}
    alt="ml prototyping"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>


1. Define variables and split the data
    - Use the `Select Columns` widget to choose the features and target.
    - Connect to `Data Sampler` to randomly split the data into two parts. Here we use 70% as the train data and the remaining as the test data.
<p>
  <img
    src={require('./img/ml_proto_sampling.png').default}
    alt="ml sampling"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>

2. Select learners and set parameters
    - In the `Model` section, pick a few classification learners and connect them to `Train Data Table`. Here we use `Logistic Regression`, `Random Forest`, and `SVM`.
    - Open each learner widget to configure the parameters for its algorithm.
<p>
  <img
    src={require('./img/ml_proto_learner.png').default}
    alt="ml prototyping learner configuration"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>

3. Configure cross-validation and view the confusion matrix
    - Connect each learner to the `Test and Score` widget in the `Evaluation` section.
    - Then connect it to the `Confusion Matrix`.
    - With the settings below, 10-fold cross-validation runs for every learner and computes key metrics such as AUC, precision, and recall—in seconds.

<p>
  <img
    src={require('./img/ml_proto_eval.png').default}
    alt="ml prototyping cv and confusion matrix"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>

4. Run inference and evaluate
    - Add the `Prediction` widget from the `Evaluate` section and connect both the `Test Data Table` and the learners.
    - Cross-validation showed that Logistic Regression and SVM performed best, so run inference with those two algorithms.
    - Create one more `Confusion Matrix` widget, connect it, and confirm the generalization performance.
