![Treat_Banner](static/images/readme-images/Treat_Banner.png)

<h1 align="center">
  Trigger Recognition for Enjoyable and Appropriate Television - EXAONE
</h1>

<p align="center">
<img src="https://img.shields.io/static/v1?label=Kuberwastaken&message=TREAT EXAONE&color=f8b2b7&logo=github" alt="Kuberwastaken - TREAT EXAONE">
<img src="https://img.shields.io/badge/version-Alpha-f8b2b7" alt="Version Alpha">
</p>

I was tired of getting grossed out watching unexpected scenes in movies and TV and losing my appetite, that's why I created TREAT.

The goal of this project is to empower viewers by forewarning them about potential triggers in the content they watch, making the viewing experience more enjoyable, inclusive, and appropriate for everyone.

TREAT is a web application that uses natural language processing to analyze movie and TV show scripts, identifying potential triggers to help viewers make informed choices.

## Installation Instructions
### Prerequisites
 - Star the Repository to Show Your Support :P
 - Clone the Repository to Your Local Machine:

    ```bash
   git clone https://github.com/Kuberwastaken/TREAT-EXAONE.git
    ```

### Environment Setup
To set up the development environment, you will need to create a virtual environment and install the necessary dependencies.

1. Create a Virtual Environment:

   ```bash
   python3 -m venv TREAT-EXAONE
   ```

2. Activate the Virtual Environment:

   ```bash
   source treat-env/bin/activate   # On Unix or MacOS
   treat-env\Scripts\activate      # On Windows
   ```

3. Install Dependencies:

   Navigate to the project directory and run:

   ```bash
   pip install -r requirements.txt
   ```

## Project Usage
1. **Start the Flask Server:**

   ```bash
   python run.py
   ```

2. **Open Your Browser:** 

   Navigate to `http://127.0.0.1:5000` to access the TREAT web interface.

3. **Analyze Scripts:**

   You can manually enter a script in the provided text area and click "Analyze Script."

## File Descriptions
- **app.py:** The main Flask application file that handles routing.

- **app/routes.py:** Contains the Flask routes for handling script uploads.

- **app/model.py:** Includes the script analysis functions using the LGAI EXAONE 2.4B model.

- **templates/index.html:** The main HTML file for the web interface.

- **static/css/style.css:** Custom CSS for styling the web interface.

- **static/js/app.js:** JavaScript for handling client-side interactions.

## Types of Triggers Detected
The TREAT application focuses on identifying a variety of potential triggers in scripts, including but not limited to:

- **Violence:** Scenes of physical aggression or harm.

- **Self-Harm:** Depictions of self-inflicted injury.

- **Death:** Depictions of death or dying characters.

- **Sexual Content:** Any depiction or mention of sexual activity, intimacy, or behavior.

- **Sexual Abuse:** Instances of sexual violence or exploitation.

- **Gun Use:** Depictions of firearms and their usage.

- **Gore:** Graphic depiction of injury, blood, or dismemberment.

- **Vomit:** Depictions of vomiting or nausea-inducing content.

- **Mental Health Issues:** Depictions of mental health struggles, including anxiety, depression, or disorders.

- **Animal Cruelty:** Depictions of harm or abuse towards animals.

These categories help address a very real-world problem by forewarning viewers about potentially distressing content, enhancing their viewing experience.

Adding new categories is as simple as specifying a new category under model.py and utils.py

## Design Choices

- **Inspiration:** I aimed for a simple and intuitive user experience, focusing on simplicity and ease of use. This decision stemmed from the need to create a tool that is easy to navigate for all users, regardless of background or age.

- **Theme and Color Scheme:** The chosen theme and color scheme create a visually appealing and engaging environment. The chocolate and sweets theme is intended to stick to the TREAT theme and make the experience enjoyable and pleasant.

- **Script Analysis:** The LGAI EXAONE 2.4B model by LG AI Research was chosen for its efficiency and performance in text analysis tasks. As part of the EXAONE 3.5 series, it offers state-of-the-art performance for its size, with support for both English and Korean languages. The model is optimized for small devices while maintaining high accuracy, making it accessible for users with limited hardware. It can handle long contexts up to 32K tokens, improving script analysis for larger texts.

## Switching Between Models
TREAT is designed to be flexible with different language models. You can easily switch between models by editing the `model.py` file:

1. Open `app/model.py`
2. Locate the model initialization section (typically at the beginning of the file)
3. Change the model name from:
   ```python
   model_name = "LGAI-EXAONE/EXAONE-3.5-2.4B-Instruct"
   ```
   
   To any other compatible model, such as:
   ```python
   model_name = "deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B"  # For DeepSeek-R1 1.5B
   ```
   or:
   ```python
   model_name = "LGAI-EXAONE/EXAONE-3.5-7.8B-Instruct"  # For larger EXAONE model
   ```

4. Adjust the prompt template if necessary to match the expected format of the new model.

5. Remember to adjust parameters like `max_chunk_size` based on the capabilities of the selected model.

# Model Configuration Guide

## Core Configuration Parameters ⚙️

### 1. Text Chunking Settings
```python
# Located in analyze_script()
max_chunk_size = 1024  # Text segment length (tokens)
overlap = 128           # Context preservation between chunks
```
#### Recommended Adjustments:
- **For long dialogues:**
  ```python
  max_chunk_size = 1536
  overlap = 256
  ```
- **For action-heavy scripts:**
  ```python
  max_chunk_size = 768
  overlap = 64
  ```

### 2. Generation Controls
```python
# In model.generate() parameters
{
    "temperature": 0.2,          # Range: 0.1 (strict) - 9.0 (creative)
    "top_p": 0.9,                # Range: 0.8 (focused) - 1.0 (diverse)
    "repetition_penalty": 1.05   # Range: 1.0 (none) - 2.0 (strict)
}
```
#### Use Case Examples:
- **For sensitive content analysis:**
  ```python
  {"temperature": 0.1, "top_p": 0.8, "repetition_penalty": 1.2}
  ```
- **For creative interpretation:**
  ```python
  {"temperature": 0.7, "top_p": 0.95, "repetition_penalty": 1.0}
  ```

### 3. Prompt Structure for LGAI EXAONE 2.4B
```python
messages = [
    {"role": "system", "content": "You are a helpful assistant specialized in analyzing scripts for trigger content."},
    {"role": "user", "content": f"""TEXT ANALYSIS:
Respond ONLY with this exact format:

VIOLENCE: [YES/NO]
...
MENTAL_HEALTH: [YES/NO]

Text: {chunk[:768]}..."""}
]
```
#### Customization Guide:
- Maintain `Respond ONLY...` directive.
- Keep category list order consistent.
- Preserve `...` after the text preview.
- Adjust the system message to refine the model's behavior if needed.

### 4. Response Parsing Logic
```python
# Category normalization
category_map = {
    cat.upper().replace("_", " "): cat 
    for cat in expected_order
}

# Answer recognition pattern
pattern = r"\b({})\b\s*[:=]\s*\[?(YES|NO|MAYBE|Y|N|M)\]?".format(
    "|".join(re.escape(cat) for cat in category_map.keys())
)
```

## Configuration Reference Table 📋

| Parameter          | Location             | Default  | Effect Range |
|--------------------|----------------------|----------|--------------|
| `max_chunk_size`   | analyze_script()     | 1024     | 512-2048     |
| `overlap`          | analyze_script()     | 128      | 32-256       |
| `temperature`      | model.generate()     | 0.2      | 0.1-1.0      |
| `top_p`            | model.generate()     | 0.9      | 0.7-1.0      |
| `repetition_penalty`| model.generate()    | 1.05     | 1.0-2.0      |

## Advanced Customization 🛠️

### Adding New Categories
1. Add to `expected_order` list:
   ```python
   expected_order = [
       ...,
       "NEW_CATEGORY"
   ]
   ```
2. Update the prompt template format section.
3. Test parsing with:
   ```python
   # Test pattern with new category
   test_text = "NEW_CATEGORY: YES"
   assert "NEW_CATEGORY" in extract_answers(test_text, expected_order)
   ```

### Modifying Response Format
Edit the regex pattern for different answer formats:
```python
# Example: Allow 'Y'/'N' shorthand
pattern = r"\b({})\b\s*[:=]\s*\[?(Y|N)\]?".format(...)
```

### Optimizing Performance
- **Reduce VRAM usage:**
  ```python
  max_chunk_size = 768
  overlap = 64
  ```
- **Faster processing (less accurate):**
  ```python
  {"temperature": 0.1, "top_p": 0.8}
  ```

## To-Do List
- Fixing the model working but not the output
- Parallel Processing of Multiple chunks
- Optimizing LGAI EXAONE 2.4B model parameters for better trigger detection
- Adding support for additional languages using the model's bilingual capabilities

## Acknowledgements
I would like to thank:

- LG AI Research: For developing and providing open access to the LGAI EXAONE models, a very critical component of this project.

- Parasite (2019): For that unexpected jumpscare that ruined my appetite and ultimately inspired this project.