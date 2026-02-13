# ComfyUI workflow notes (developer reference)

This file documents the structure and key settings for the workflow JSON.

## Top-level structure
- id: workflow UUID
- revision: workflow revision number
- last_node_id: last allocated node id
- last_link_id: last allocated link id
- nodes: list of nodes (each is a graph node)
- links: list of link tuples
- groups, config: empty in this workflow
- extra: editor metadata (ui scale, offsets, node versions, flags)
- version: workflow schema version

## Link tuple format
Each link entry is:
[link_id, from_node_id, from_slot, to_node_id, to_slot, type]

## Nodes overview
- 193 CheckpointLoaderSimple: loads base checkpoint, CLIP, VAE
  - widgets_values: z-image-turbo-fp8-aio.safetensors
- 197 LoraLoader: applies LoRA to model and clip
  - widgets_values: rite-of-wrong-z-image-60229.safetensors, 0.8 (model), 1 (clip)
- 191 ModelSamplingAuraFlow: sampling tweak for the model
  - widgets_values: 7
- 6 CLIPTextEncode (positive prompt): main prompt text
  - widgets_values: long prompt that ends with a LoRA trigger line
- 198 CLIPTextEncode (negative prompt): negative prompt
  - widgets_values: "blurry, drawing, illustration"
- 162 EmptySD3LatentImage: base latent size
  - widgets_values: 1280 x 1536, batch 1
- 163 KSampler: main sampler
  - widgets_values: seed, seed mode, steps, cfg, sampler, scheduler, denoise
- 164 VAEDecode: decodes latent to image
- 171 SaveImage: saves output
  - widgets_values: "img"
- 195 Note: "Lora Trigger Word" placeholder
- 196 MarkdownNote: instructions for models and nodes
- 199 Note: latent size suggestions

## Notes
- The "//" inside the prompt is literal prompt text, not a JSON comment.
- If you update node ids, update link tuples to keep the graph valid.
