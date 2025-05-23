# Qupath helper scripts for comet tissue scan analysis
This project contains some scripts to help with tissue analysis in qupath.

## Citing this work
Please check [CITATION.cff](CITATION.cff) for citing information in case you use this repository in your research.

## How to use the scripts
These scripts are to be used with an image with the required annotations opened in the qupath application and then using the script from the `Automate` menu.
See https://qupath.readthedocs.io/en/stable/docs/scripting/overview.html#custom-scripts for some more info.

## Scripts in this project
### [scripts/add-liver-tumor-bands-annotations.groovy](scripts/add-liver-tumor-bands-annotations.groovy)

#### Basic workings of the script
This script does the following in the case of no capsule:
- At the start it removes all annotations that were previously made by this script
    - This means all the annotations with the classification `Auto`
- It checks all annotations to find `Area` annotations with 1 or 2 line annotations that intersect it.
    - This means you need to have manually drawn an area and then manually drawn a line through this area, intersecting it on both sides.
- For these annotations, it uses the line(s) to split the geometry of this annotation into 2 parts.
- Then it checks these parts to find which one is a tumor part by looking for an annotation within it that has the classification `Tumor`
    - This means you need to have manually added some small annotation somewhere in the tumor tissue and have classified it with the name `Tumor`
- After this step, it expands the tumor and liver with band sizes that can be specified at the start of the script (see `liverBands` and `tumorBands`)
    - Then the expansion is modified to only keep the part that intersects with the other tissue (otherwise the bands would go all around instead of being constrained by the given tissue annotation)
- Of all these created parts, annotations are added with an `Auto` classification.

#### Case of tissue with Capsule
In case there is a capsule, you need to manually annotate both sides of the capsule with lines intersecting with the tissue.
It is very important that these 2 lines do not touch or intersect each other. Also, it is best that the ends of the lines are similar in direction and distance from the edge of the tissue.

So in this case that the script detects 2 lines going through a tissue annotation, it will do the following additional steps:
- It will draw a line annotation through the middle of the capsule, trying to divide the capsule into 2 parts.
- It will add annotations for each half of the capsule.
- The script will take into account the capsule for the expansion bands mentioned above.

#### Difficulties
The script has known difficulties with cases of an annotation where part of it are completely disconnected. Also if a part gets disconnected after the line splitting, this can cause problems.
To somewhat remedy this, we have added "00_ignored" annotations for these parts to indicate which parts of the tissue has been ignored.

## Development
To make script development easier, we use IntelliJ IDE and we have set the qupath app dir as library in the IDE module settings.
The app dir path in case of running qupath from the [repository](https://github.com/qupath/qupath) looks like this: `qupath/build/dist/QuPath-0.6.0-rc5-arm64.app/Contents/app`.