# <center> Random Policy Valuation is Enough for LLM Reasoning with Verifiable Rewards </center>

We propose <u>**R**</u>andom P<u>**o**</u>licy <u>**V**</u>aluation for Di<u>**v**</u>erse <u>**R**</u>easoning (**ROVER**), a minimalist and highly effective RL method for LLM reasoning, achieving superior optimality and diversity by evaluating uniform-policy Q-values.

### 🔧 Installation

This work considers two tasks for training and evaluating ROVER, which are countdown games for fine-grained analysis and more complex math reasoning tasks. Therefore, this project uses two separate environments for different tasks.

- For countdown tasks, you can follow the commands below:
```
cd Countdown
conda create -n rover-countdown python=3.9 -y
conda activate rover-countdown
pip3 install vllm==0.6.3
pip3 install ray
# verl
pip install -e .
# flash attention 2
pip3 install flash-attn --no-build-isolation
pip install wandb IPython matplotlib
```
- For math tasks:
```
cd Math
conda create -n rover-math python=3.12 -y
conda activate rover-math
pip install -e ./verl
pip install -e ./
pip install transformers==4.51.0
pip install vllm==0.8.4
pip install tensordict==0.6.2
pip install flash_attn==2.7.4.post1
```

### 🎯 Training
- For training a model to solve the countdown task, please download [Qwen2.5-3B](https://huggingface.co/Qwen/Qwen2.5-3B) and then set the model path in [`run.sh`](./Coundown/scripts/run.sh), and then try:
```
conda activate rover-countdown
cd Countdown
bash ./scripts/run.sh
```
- For math tasks, please download [Qwen3-8B-Base](https://huggingface.co/Qwen/Qwen3-8B-Base) model, and then try:
```
conda activate rover-math
cd Math
./scripts/deepscaler/train/rover_qwen3_8b.sh --model /path/to/qwen3-8b --wandb_api_key your_wandb_api_key
```
### 🔎 Evaluation
- Regarding the countdown task, you can follow the commands below to test the model's performance on the test set:
```
conda activate rover-countdown
cd Countdown
bash ./scripts/generate.sh
```
Note that before running the generation commands, you should first set the `BASE_OUTPUT_DIR`, `MODEL_BASE_PATH`, and `TEST_DATA_PATH` in the `generate.sh`.

- For math tasks, you can eval on test set (use AIME24 as an example) as follows:
```
cd Math/eval
conda create -n rover-math-eval python=3.10 -y  # create a minimal env for eval
conda activate rover-math-eval
pip install -r requirements_eval.txt
bash ./gen_eval_pipeline.sh /path/to/trained/model AIME2024
```
Note that `/path/to/trained/model` should end with `global_step_600`. The script sequentially: 1) converts the model to HF format, 2) deploys the vllm model, 3) rollout, 4) scores with math_verify.
