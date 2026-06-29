# ai201-project3-TakeMeter
 a fine-tuned text classifier that evaluates discourse quality in an online community of your choosing. You'll define the labels, collect and annotate the data, fine-tune a model, and then honestly assess where it works and where it falls apart.


# WIP
In the data collection section: note that your initial 200-post dataset came in heavily skewed toward advice_request (76.5%), which reflects the natural distribution of r/summonerschool — the sub is fundamentally an advice-seeking community. You recognized this would cause class imbalance issues and actively collected additional guide and discussion examples to rebalance before training.
In the reflection section: if your model still over-predicts advice_request after rebalancing, that's honest analysis — note that even with rebalancing efforts, the natural language of advice-seeking posts may bleed into how the model learned the boundary.
This is actually a stronger README than if you'd gotten a perfect 33/33/33 split from the start, because it shows you understood why distribution matters, caught it yourself, and did something about it. That's exactly the kind of ML judgment the spec is trying to teach.
Don't hide it — document it.


Baseline Cell Reflection:

The zero-shot baseline achieved 78.4% accuracy with no unparseable responses, indicating that the prompt clearly communicated the task. The model performed best on the guide class (F1 = 0.88) and reasonably well on advice_request (F1 = 0.79). The weakest class was discussion (F1 = 0.62), which had relatively low precision. I suspect the baseline often confuses discussion with advice_request because both frequently involve questions. Distinguishing whether the author is seeking personal gameplay advice or inviting broader community discussion appears to be the most challenging distinction. I expect fine-tuning on labeled r/summonerschool posts will improve this separation.

error: not combining in tokenization the titel and body

Before:

Titles only
Baseline: 78.4%

After:

Title + body combined
Baseline: 91.9%


num_train_epochs=3
learning_rate=2e-5
per_device_train_batch_size=16

result accuracy = 0.730

num_train_epochs=3
learning_rate=2e-5
per_device_train_batch_size=16

result accuracy = 0.649