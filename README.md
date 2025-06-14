
# LLaMA 3.2-1B Instruction-Tuned for English-Spanish Translation

This project fine-tunes LLaMA 3.2-1B using QLoRA (Quantized LoRA) to translate from English to Spanish. The goal was to teach the model to follow a structured translation prompt and produce clean translations without unnecessary or repeated output. 

I used about 1000 sentence pairs for instruction tuning and found that to be sufficient for demonstrating the effectiveness of this method. Model took 30 minutes to fine-tune on Google Colab's T4 GPU.

## Key details

- **Base model**: meta-llama/Llama-3.2-1B
- **Method**: LoRA (r=16, alpha=32, dropout=0.05) with 4-bit quantization
- **Dataset**: 1000 sentence pairs (OPUS Books, English-Spanish)
- **Training setup**:
  - 2 epochs
  - batch size 4
  - bfloat16 mixed precision

## Example output comparison

**Input**:  
Will it hurt when I get my aligners?

**Base model**:
Translate this from English to Spanish:

English: Will it hurt when I get my aligners?

Spanish: ¿Sarará doloroso cuando me ponga mis ortodoncias?

The answer is yes, but it will be a lot less painful than you think. The aligners are made of a material that is very similar to the material that teeth are made of. The aligners are made of a material that is...

**Tuned model**:
Translate this from English to Spanish:

English: Will it hurt when I get my aligners?

Spanish: ¿Sarará doloroso cuando me ponga mis ortodoncias?

---

**Input**:  
I lost my last tray. What should I do?

**Base model**:
Translate this from English to Spanish:

English: I lost my last tray. What should I do?

Spanish: ¿Qué debo hacer ahora que he perdido mi última bandeja?

I lost my last tray. What should I do?
I lost my last tray. What should I do?
I lost my last tray. What should I do?
I lost my last tray. What should I do?
I lost my last tray

**Tuned model**:
Translate this from English to Spanish:

English: I lost my last tray. What should I do?

Spanish: Perdí mi última bandeja. ¿Qué hago?

## Evaluation
As we can see, the base LLaMA 3.2-1B model doesn't know where to stop generating and produces a lot of unnecessary output. On the other hand ourr fine-tuned model consistently follows the instruction format, cleanly producing the translation and stopping at the right point. This shows how instruction tuning (even on a small dataset) can effectively teach the model the desired behavior and correct issues like over-generation.

## Prompt Formatting

I designed the prompt to look like this:
```
Translate the following text from English to Spanish:
English: <input text>
Spanish:
```
Then I trained the model to complete the Spanish part of the prompt. I applied LoRA to the main attention and MLP layers, and used quantization to make it more efficient to train. See `translation_tuning.ipynb` for the full code to load, train, and evaluate the model.

