# OCR-D Glossary

> Glossary of terms from the domain of image processing/OCR and how they are used within the OCR-D framework

This section is non-normative.

## Layout and Typography

### Block

A block is a region described by a polygon inside a page.

### Block type

The semantics or function of a [block](#block) such as heading, page number, column, table...

### Font family

Within OCR-D, *font family* refers to grouping elements by font similarity. The
semantics of a *font family* are up to the data producer.

### Glyph

Within OCR-D, a glyph is the atomic unit within a [word](#word).

### Grapheme Cluster

See [Glyph](#glyph)

### Line

See [TextLine](#textline)

### Reading Order

Reading order describes the logical sequence of [blocks](#block) within a document.

### Region

See [Block](#block)

### Symbol

See [Glyph](#glyph)

### TextLine

A TextLine is a [block](#block) of text without line break.

### Word

A word is a sequence of [glyphs](#glyph) not containing any word-bounding whitespace.

## Data

### Ground Truth

Ground truth (GT) [in the context of OCR-D](http://ocr-d.de/daten) are
transcriptions, specific structure descriptions and word lists. These are
essentially available in PAGE XML format in combination with the original
image. Essential parts of the GT were created manually.

We distinguish different usage scenarios for GT:

#### Reference data

With the term *reference data*, we refer to data that illustrates
different stages of an OCR/OLR process on representative materials. They are
supposed to support the assessment of commonly encountered difficulties and challenges when
running certain analysis operations and are therefore manually annotated
at all levels.

#### Evaluation data

*Evaluation data* are used to quantitatively evaluate the performance of OCR tools
and/or algorithms. Parts of these data which correspond to the tool(s) under consideration
are guaranteed to be recorded manually.

#### Training data

Many OCR-related tools need to be adapted to the specific domain of the works which are to
be processed. This domain adaptation is called *training*. Data used to guide this process
are called *training data*. It is essential that those parts of these data which are fed
to the training algorithm are captured manually.

## Activities

### Binarization

Binarization means converting all color or grayscale pixels in an image to either black or white.

Controlled term: `binarized` (`comments` of a mets:file), `preprocessing/optimization/binarization` (`step` in ocrd-tool.json)

See [Felix' Niklas interactive demo](http://felixniklas.com/imageprocessing/binarization)

### Dewarping

Manipulate an image in such a way that all text lines are
straightened and any geometrical distortions have been corrected.

Controlled term: `preprocessing/optimization/dewarping`

See [Matt Zucker's entry on Dewarping](https://mzucker.github.io/2016/08/15/page-dewarping.html).

### Despeckling

Remove artifacts such as smudges, ink blots, underlinings etc. from an image. Typically applied to 
remove "salt-and-pepper" noise resulting from [Binarization](#Binarization).

Controlled term: `preprocessing/optimization/despeckling`

### Deskewing

Rotate an image so that all text lines are horizontal.

Controlled term: `preprocessing/optimization/deskewing`

### Font indentification

Detect the font type(s) used in the document, either before or after an OCR run.

Controlled term: `recognition/font-identification`

### Grayscale normalization

> ISSUE: https://github.com/OCR-D/spec/issues/41

Controlled term:
  - `gray_normalized` (`comments` in file)
  - `preprocessing/optimization/cropping` (step)

Gray normalization is similar to binarization but instead of a purely bitonal
image, the output can also contain shades of gray to avoid inadvertently
combining glyphs when they are very close together.

### Document analysis

Document analysis is the detection of structure on the document level to e.g. create a table of contents.

### Reading order detection

Detect the [reading order](#reading-order) of [blocks](#block).

### Cropping

Detecting the print space in a page, as opposed to the margins. It is a form of
[block segmentation](#block-segmentation).

Controlled term: `preprocessing/optimization/cropping`.

### Border removal

--> [Cropping](#cropping)

### Segmentation

Segmentation means detecting areas within an image.

Specific segmentation algorithms are labelled by the semantics of the regions
they detect not the semantics of the input, i.e. an algorithm that detects
blocks is called [block segmentation](#block-segmentation).

### Block segmentation

Segment an image into [blocks](#block). Also determines whether this is a text
or non-text block (e.g. images).

Controlled term:
- `SEG-BLOCK` (`USE`)
- `layout/segmentation/region` (step)

### Block classification

Determine the [type](#block-type) of a detected block.

### Line segmentation
Segment text [blocks](#block) into [textlines](#textline).

Controlled term:
- `SEG-LINE` (`USE`)
- `layout/segmentation/line` (step)

### MP

Module Project, a software project producing one or more tools to OCR-D. There
are [currently eight MP](https://ocr-d.github.io/projects) active in the OCR-D
community.

### OCR

Map pixel areas to [glyphs](#glyph) and [words](#words).

### Word segmentation

Segment a [textline](#textline) into [words](#word)

Controlled term:
- `SEG-LINE` (`USE`)
- `layout/segmentation/word` (step)

### Glyph segmentation

Segment a [textline](#textline) into [glyphs](#glyph)

Controlled term: `SEG-GLYPH`

### Text recognition

See [OCR](#ocr).

### Text optimization

Text optimization encompasses the manipulations to the text based on the steps
up to and including text recognition. This includes (semi-)automatically correcting
recognition errors, orthographical harmonization, fixing segmentation errors etc.

## Data Persistence

### Software repository

The software repository contains all OCR-D algorithms and tools developed
during the project including tests. It will also contain the documentation and
installation instructions for deploying a document analysis workflow.

### Ground Truth repository

Contains all the [ground truth](#ground-truth) data.

### Research data repository

The research data repository may contain the results of all
[activities](#activities) during document analysis. At least it contains the
end results of every processed document and its full provenance. The research
data repository must be available locally.

### Model repository

Contains all trained (OCR) models for text recognition. The model repository
has to be available at least locally. Ideally, a publicly available model repository will
be developed.

## OCR-D modules

The [OCR-D project](https://ocr-d.de) divided the various elements of an OCR
workflow into six modules.

### Image preprocessing

Manipulating the input images for subsequent layout analysis and text recognition.

### Layout analysis

Detection of structure within the page.

### Text recognition and optimization

Recognition of text and post-correction of recognition errors.

### Model training

Generating data files from aligned ground truth text and images to configure
the prediction of text and layout recognition engines.

### Long-term preservation and persistence

Storing results of OCR and OLR indefinitely, taking into account versioning,
multiple runs, provenance/parametrization and providing access to these saved
snapshots in a granular fashion.

### Quality assurance

Providing measures, algorithms and software to estimate the quality of the [individual processes](#activities) within the OCR-D domain.
