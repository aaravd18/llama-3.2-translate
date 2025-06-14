
# LLaMA 3.2-1B Instruction-Tuned for English-Spanish Translation

This project fine-tunes LLaMA 3.2-1B using LoRA (with quantization) to translate from English to Spanish. The goal was to teach the model to follow a structured translation prompt and produce clean, bounded translations without unnecessary or repeated output. 

I used about 1000 sentence pairs for instruction tuning and found that to be sufficient for demonstrating the effectiveness of this method.

## Key details

- **Base model**: meta-llama/Llama-3.2-1B
- **Method**: LoRA (r=16, alpha=32, dropout=0.05) with 4-bit quantization (nf4, double quantization)
- **Dataset**: 1000 sentence pairs (OPUS Books, English-Spanish)
- **Training setup**:
  - 2 epochs
  - batch size 4 (with gradient accumulation)
  - bfloat16 mixed precision

## Example output comparison

**Input**  
Will it hurt when I get my aligners?

**Base model**
Translate this from English to Spanish:

English: Will it hurt when I get my aligners?

Spanish: ¿Sarará doloroso cuando me ponga mis ortodoncias?

The answer is yes, but it will be a lot less painful than you think. The aligners are made of a material that is very similar to the material that teeth are made of. The aligners are made of a material that is...

**Tuned model**
Translate this from English to Spanish:

English: Will it hurt when I get my aligners?

Spanish: ¿Sarará doloroso cuando me ponga mis ortodoncias?

---

**Input**  
I lost my last tray. What should I do?

**Base model**
Translate this from English to Spanish:

English: I lost my last tray. What should I do?

Spanish: ¿Qué debo hacer ahora que he perdido mi última bandeja?

I lost my last tray. What should I do?
I lost my last tray. What should I do?
I lost my last tray. What should I do?
I lost my last tray. What should I do?
I lost my last tray

**Tuned model**
Translate this from English to Spanish:

English: I lost my last tray. What should I do?

Spanish: Perdí mi última bandeja. ¿Qué hago?

## How it works

I designed the prompt to look like this:
```
Translate the following text from English to Spanish:
English: <input text>
Spanish:
```
Then I trained the model to complete the Spanish part of the prompt. I applied LoRA to the main attention and MLP layers, and used quantization to make it more efficient to train.

## How to run

Install the dependencies:
```
pip install transformers peft bitsandbytes datasets sentencepiece sacrebleu evaluate
```
See `translation_tuning.ipynb` for the full code to load, train, and evaluate the model.

## Results

The instruction-tuned model:
- Stops generation at the right place
- Follows the prompt cleanly
- Avoids unnecessary repetitions

## Future work

Some next steps I’m considering:
- Expanding the dataset with domain-specific sentence pairs (e.g. dental, medical)
- Tuning for other language pairs
- Distilling the model for cheaper deployment
