# Review Rubric

Score each dimension from 0 to 100.

The run passes only when every dimension is above 80.

## Dimensions

### Scientific accuracy

Does the figure communicate the intended scientific meaning correctly?

### Layout clarity

Is the spatial organization easy to follow?

### Visual consistency

Do shapes, colors, arrows, and emphasis rules look coherent?

### Text readability

Are labels and annotations readable and appropriately concise?

### Alignment and spacing quality

Are objects aligned and spaced with enough visual discipline?

### Connector and flow quality

Do arrows and links communicate the intended flow cleanly?

### Draw.io compatibility

Can the XML be imported into `draw.io` or `diagrams.net` without obvious structural failure?

### Editability

Can a human easily edit text, shapes, connectors, and grouped elements after import?

### Asset usage quality

If assets are included, are they integrated at sensible scale and placement?

### Overall completeness

Does the output feel finished enough to hand off for editing rather than reconstruction?

## Failure Routing

- layout issues -> `architect`
- style or color issues -> `drawer`
- copy issues -> `writer`
- structure or editability issues -> `xml-drawer`

