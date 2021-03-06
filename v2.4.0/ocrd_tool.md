# ocrd-tool.json

Tools MUST be described in a file `ocrd-tool.json` in the root of the repository.

It must contain a JSON object adhering to the [ocrd-tool JSON Schema](#Definition).

In particular, every tool provided must be described in an array item under the
`tools` key. These definitions drive the [CLI](cli) and the [web
services](swagger).

To validate a `ocrd-tool.json` file, use `ocrd ocrd-tool /path/to/ocrd-tool.json validate`.

## File parameters

To mark a parameter as expecting the adress of a file, it must declare the
`content-type` property as a [valid media
type](https://www.iana.org/assignments/media-types/media-types.xhtml).
Optionally, workflow processors can be notified that this file is potentially
large and static (e.g. a fixed dataset or a precomputed model) and should be cached
indefinitely after download by setting the `cacheable` property to `true`.

## Definition

<!-- Regenerate with 'shinclude -i ocrd_tool.md'. See https://github.com/kba/shinclude -->
<!-- BEGIN-EVAL -w '```yaml' '```' -- cat ./ocrd_tool.schema.yml -->
```yaml
type: object
description: Schema for tools by OCR-D MP
required:
  - version
  - git_url
  - tools
additionalProperties: false
properties:
  version:
    description: "Version of the tool, expressed as MAJOR.MINOR.PATCH."
    type: string
    pattern: '^[0-9]+\.[0-9]+\.[0-9]+$'
  git_url:
    description: Github/Gitlab URL
    type: string
    format: url
  dockerhub:
    description: DockerHub image
    type: string
  tools:
    type: object
    additionalProperties: false
    patternProperties:
      'ocrd-.*':
        type: object
        additionalProperties: false
        required:
          - description
          - steps
          - executable
          - categories
        properties:
          executable:
            description: The name of the CLI executable in $PATH
            type: string
          parameters:
            description: Object describing the parameters of a tool. Keys are parameter names, values sub-schemas.
            type: object
            patternProperties:
              ".*":
                type: object
                additionalProperties: false
                required:
                  - description
                  - type
                  # also either 'default' or 'required'
                properties:
                  type:
                    type: string
                    description: Data type of this parameter
                    enum:
                      - string
                      - number
                      - boolean
                  format:
                    description: Subtype, such as `float` for type `number` or `uri` for type `string`.
                  description:
                    description: Concise description of syntax and semantics of this parameter
                  required:
                    type: boolean
                    description: Whether this parameter is required
                  default:
                    description: Default value when not provided by the user
                  enum:
                    type: array
                    description: List the allowed values if a fixed list.
                  content-type:
                    type: string
                    description: "If parameter is reference to file: Media type of the file"
                    pattern: '^[a-z0-9\._-]+/[A-Za-z0-9\._\+-]+$'
                  cacheable:
                    type: boolean
                    description: "If parameter is reference to file: Whether the file should be cached, e.g. because it is large and won't change."
                    default: false
          description:
            description: Concise description what the tool does
          categories:
            description: Tools belong to this categories, representing modules within the OCR-D project structure
            type: array
            items:
              type: string
              enum:
                - Image preprocessing
                - Layout analysis
                - Text recognition and optimization
                - Model training
                - Long-term preservation
                - Quality assurance
          steps:
            description: This tool can be used at these steps in the OCR-D functional model
            type: array
            items:
              type: string
              enum:
                - preprocessing/characterization
                - preprocessing/optimization
                - preprocessing/optimization/cropping
                - preprocessing/optimization/deskewing
                - preprocessing/optimization/despeckling
                - preprocessing/optimization/dewarping
                - preprocessing/optimization/binarization
                - preprocessing/optimization/grayscale_normalization
                - recognition/text-recognition
                - recognition/font-identification
                - recognition/post-correction
                - layout/segmentation
                - layout/segmentation/region
                - layout/segmentation/line
                - layout/segmentation/word
                - layout/segmentation/classification
                - layout/analysis
```

<!-- END-EVAL -->

## Example

This is from the [ocrd_tesserocr sample project](https://github.com/OCR-D/ocrd_tesserocr):

<!-- BEGIN-EVAL -w '```json' '```' -- cat ../ocrd_kraken/ocrd-tool.json -->
```json
{
  "git_url": "https://github.com/OCR-D/ocrd_kraken",
  "version": "0.0.1",
  "tools": {
    "ocrd-kraken-binarize": {
      "executable": "ocrd-kraken-binarize",
      "categories": [
        "Image preprocessing"
      ],
      "steps": [
        "preprocessing/optimization/binarization"
      ],
      "description": "Binarize images with kraken",
      "parameters": {
        "level-of-operation": {
          "type": "string",
          "default": "page",
          "enum": ["page", "block", "line"]
        }
      }
    },
    "ocrd-kraken-segment": {
      "executable": "ocrd-kraken-segment",
      "categories": [
        "Layout analysis"
      ],
      "steps": [
        "layout/segmentation/region"
      ],
      "description": "Block segmentation with kraken",
      "parameters": {
        "text_direction": {
          "type": "string",
          "description": "Sets principal text direction",
          "enum": ["horizontal-lr", "horizontal-rl", "vertical-lr", "vertical-rl"],
          "default": "horizontal-lr"
        },
        "script_detect": {
          "type": "boolean",
          "description": "Enable script detection on segmenter output",
          "default": false
        },
        "maxcolseps": {"type": "number", "format": "integer", "default": 2},
        "scale": {"type": "number", "format": "float", "default": null},
        "black_colseps": {"type": "boolean", "default": false},
        "white_colseps": {"type": "boolean", "default": false}
      }
    },
    "ocrd-kraken-ocr": {
      "executable": "ocrd-kraken-ocr",
      "categories": ["Text recognition and optimization"],
      "steps": [
        "recognition/text-recognition"
      ],
      "description": "OCR with kraken",
      "parameters": {
        "lines-json": {
          "type": "string",
          "format": "url",
          "required": "true",
          "description": "URL to line segmentation in JSON"
        }
      }
    }

  }
}
```

<!-- END-EVAL -->


