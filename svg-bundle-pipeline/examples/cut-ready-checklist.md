# Cut-ready checklist

Run through this list before you deliver any SVG. Every item maps to a standard in SKILL.md or in `references/svg-master-pipeline.md`.

## Before delivery

- [ ] Every path subpath closes with Z
- [ ] Colors are the exact hex codes from the palette or the original image, or single-fill `#111827` when no palette was given
- [ ] A compound stencil carries fill-rule="evenodd"
- [ ] A complex stencil is one compound path, not several overlapping paths
- [ ] Each color layer is its own path or group element, ordered back-to-front
- [ ] The file is well-formed XML; the validator parses it
- [ ] No specks under the pipeline dust limit (area under 350 in 4x space and perimeter under 10.0 in 1x space)
- [ ] The silhouette was rendered and looked at
- [ ] Node count under about 1,000 for HTV, about 1,500 for permanent vinyl
- [ ] No stroke attributes with a real color anywhere in the file
- [ ] Overlapping paths in the same fill are welded or subtracted
- [ ] No acute angles under 30 degrees on short spikes
- [ ] viewBox is exactly `0 0 1024 1024`
- [ ] Design normalized to 870 px, centered at (512, 512), with an 8 px clear border

## Validator

Run the premium validator if you have it:

```bash
python3 validate.py your-file.svg
```

Fix every error. Read every warning and decide on it; note the decision in the delivery note. A file with errors is not ready to deliver.

## Delivery note

State in the delivery note: the format (stencil or layered), the canvas and viewBox, and that no cut test was run. The buyer test-cuts on their own machine.
