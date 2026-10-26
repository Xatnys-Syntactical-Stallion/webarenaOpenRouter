# Custom Tests / Models with OpenArena

Goal: run webarena with free keys from OpenRouter using a custom test suite.  This is a fork of WebArena.

## Steps

First, clone this repo and cd into the directory.
```bash
conda create -n webarena python=3.10; conda activate webarena
pip install -r requirements.txt
playwright install
pip install -e .
```

Create a key with a $0 limit on OpenRouter, copy that key and export it as the environment variable `OPENAI_API_KEY`. 

For the purposes of this test, I created a folder `webarena/environment_docker/agnesblog`.  It contains a flask app that hosts a static webpage.

Host your custom sites and the homepage on localhost.  To host the sites, I used:
```bash
# In a new terminal window
cd webarena-homepage
flask run --host=127.0.0.1 --port=4399
# In a new terminal window
cd ../agnesblog
flask run --host=127.0.0.1 --port=4400
# In your original webarena environment.
export HOMEPAGE="http://127.0.0.1:4399"
export BLOG="http://127.0.0.1:4400"
```
Now, we can generate the tests.

```bash
python scripts/generate_test_data.py
mkdir test_res
python run.py --instruction_path agent/prompts/jsons/p_cot_id_actree_2s.json --test_start_idx 0 --test_end_idx 1 --model nvidia/nemotron-3-ultra-550b-a55b:free --result_dir test_res
```

Notes:
 - If you run a test, you should remove all contents from test_res before running another.  You also do not need to call generate_test_data if you did not add a new test.
 - The actual model answers are contained in test_res/render_0.html.  You will have to remove the base64 image in order to see the response in the browser.
 - The models that OpenRouter provides for free appear to change over time.  You can find the current list [here](https://openrouter.ai/collections/free-models).
 - The test_res directory was omitted from this repository.
