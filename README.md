# Septuagint Apparatus Parser

A Python application that processes the textual version of the Septuagint apparatus and converts it into a data structure compatible with the Critical Edition Ontology (CEO) in JSON format. This tool aims to enhance the usability of the Septuagint and its apparatus by researchers, easing the learning and understanding of the Septuagint in the religious studies research community.

## Introduction

The Septuagint Apparatus Parser reads apparatus entries from a text file, parses the information according to predefined rules and abbreviation mappings, and outputs a structured JSON file. The parser is designed to handle complex textual annotations found in critical editions of the Septuagint, making it easier for researchers to analyze textual variations and witness information.

## Features

- Parses complex apparatus entries, including Greek text, witnesses, and variant readings.
- Handles abbreviation mappings for types, causes, and notes.
- Supports processing of special cases like overlined characters, homoioteleuton, and transpositions.
- Outputs a JSON file structured according to the Critical Edition Ontology (CEO).

## Installation

### Prerequisites

- Python 3.6 or higher
- pip package manager

### Required Python Packages

- `pandas`
- `rdflib`

### Install Required Packages

You can install the required packages using the following command:

```bash
pip install pandas rdflib
```

## Usage

### Input Files

1. **Apparatus Text File (`AppGenesisComplete.txt`):**
   - A text file containing the apparatus entries to be parsed.
   - Each line corresponds to a verse and contains apparatus elements related to that verse.

2. **Type Cause Mapping File (`TypeCauseNote.csv`):**
   - A CSV file containing abbreviation mappings for types, causes, and notes.
   - The file should have columns: `Abbreviation`, `Type`, `Meaning`, `Value`.

3. **Turtle File (`witness_genesis_20240924.ttl`):**
   - A TTL (Turtle) file containing witness information in RDF format.
   - Used to extract detailed witness information during parsing.

### Output File

- **JSON Output File (`GottAppParser_output_complete_20240929.json`):**
  - The generated JSON file containing the parsed apparatus data structured according to the CEO.

### Running the Parser

1. **Clone the Repository**

   ```bash
   git clone https://github.com/yourusername/septuagint_apparatus_parser.git
   cd septuagint_apparatus_parser
   ```

2. **Place Input Files**

   Ensure that the following files are placed in the project directory:

   - `AppGenesisComplete.txt`
   - `TypeCauseNote.csv`
   - `witness_genesis_20240924.ttl`

3. **Run the Parser**

   ```bash
   python septuagint_apparatus_parser.py
   ```

   The script will process the input files and generate the output JSON file `GottAppParser_output_complete_20240929.json`.

### Command-Line Arguments

You can modify the file paths by editing the `__main__` section of the script:

```python
if __name__ == "__main__":
    parser = SeptuagintApparatusParser(
        bible_file_path="AppGenesisComplete.txt",
        typecause_file_path="TypeCauseNote.csv",
        output_file_path="GottAppParser_output_complete_20240929.json"
    )
    parser.process_file()
```

### Example

Given an apparatus entry in `AppGenesisComplete.txt`:

```
1:1 [ἐν ἀρχῇ] TR; [ἐν ἀρχῇ ἐποίησεν] 𝔐, A, B; [ἐποίησεν] D
```

The parser will generate a structured JSON output capturing the variation units, readings, witnesses, and any associated notes.

## Output Structure

The output JSON file will have the following structure:

```json
{
  "bibles": {
    "Septuagint": {
      "books": {
        "Genesis": {
          "chapters": {
            "1": {
              "verses": {
                "1": [
                  {
                    "cao:VariationUnit": {
                      "cao:VariationLocation": {
                        "oa:hasSelector": {
                          "oa:TextQuoteSelector": {
                            "oa:exact": "ἐν ἀρχῇ"
                          }
                        },
                        "cao:isWitnessedBy": {
                          "TR": {
                            "ceo:witnessIsIdentifiedBy": "TR"
                          }
                        }
                      },
                      "cao:hasReading": [
                        {
                          "cao:Reading": "ἐν ἀρχῇ",
                          "cao:isWitnessedBy": {
                            "TR": {
                              "ceo:witnessIsIdentifiedBy": "TR"
                            }
                          }
                        }
                      ]
                    }
                  },
                  {
                    "cao:VariationUnit": {
                      "cao:VariationLocation": {
                        "oa:hasSelector": {
                          "oa:TextQuoteSelector": {
                            "oa:exact": "ἐν ἀρχῇ ἐποίησεν"
                          }
                        },
                        "cao:isWitnessedBy": {
                          "𝔐": {
                            "ceo:witnessIsIdentifiedBy": "𝔐"
                          },
                          "A": {
                            "ceo:witnessIsIdentifiedBy": "A"
                          },
                          "B": {
                            "ceo:witnessIsIdentifiedBy": "B"
                          }
                        }
                      },
                      "cao:hasReading": [
                        {
                          "cao:Reading": "ἐν ἀρχῇ ἐποίησεν",
                          "cao:isWitnessedBy": {
                            "𝔐": {
                              "ceo:witnessIsIdentifiedBy": "𝔐"
                            },
                            "A": {
                              "ceo:witnessIsIdentifiedBy": "A"
                            },
                            "B": {
                              "ceo:witnessIsIdentifiedBy": "B"
                            }
                          }
                        }
                      ]
                    }
                  },
                  {
                    "cao:VariationUnit": {
                      "cao:VariationLocation": {
                        "oa:hasSelector": {
                          "oa:TextQuoteSelector": {
                            "oa:exact": "ἐποίησεν"
                          }
                        },
                        "cao:isWitnessedBy": {
                          "D": {
                            "ceo:witnessIsIdentifiedBy": "D"
                          }
                        }
                      },
                      "cao:hasReading": [
                        {
                          "cao:Reading": "ἐποίησεν",
                          "cao:isWitnessedBy": {
                            "D": {
                              "ceo:witnessIsIdentifiedBy": "D"
                            }
                          }
                        }
                      ]
                    }
                  }
                ]
              }
            }
          }
        }
      }
    }
  }
}
```

## Contributing

We welcome contributions from the community. Please follow these guidelines:

- **Fork the Repository:** Click on the "Fork" button at the top right corner of this page to create a copy of the repository on your GitHub account.

- **Create a New Branch:** It's good practice to create a new branch for each feature or bug fix:

  ```bash
  git checkout -b feature/your-feature-name
  ```

- **Make Your Changes:** Improve the code, add features, or fix bugs.

- **Ensure Code Quality:**

  - Follow PEP8 standards for Python code.
  - Add docstrings and comments where necessary.
  - Test your changes thoroughly.

- **Commit and Push:**

  ```bash
  git add .
  git commit -m "Description of your changes"
  git push origin feature/your-feature-name
  ```

- **Submit a Pull Request:** Go to your fork on GitHub and click on the "New Pull Request" button. Provide a detailed description of your changes.

## License

This project is licensed under the GNU General Public License v3.0. See the [LICENSE](LICENSE) file for details.

## Contact

For any questions or suggestions, please open an issue on the GitHub repository.

## Acknowledgements

- The [Critical Edition Ontology (CEO)](http://example.org/ceo/) for providing a structured framework.
- The religious studies research community for inspiring this project.
- [This article](https://abramkj.com/2013/01/11/how-to-read-and-understand-the-gottingen-septuagint-a-short-primer-part-2-apparatus/) for guidance on understanding the Göttingen Septuagint apparatus.

---

**Note:** This parser is designed to work with specific formats of the Septuagint apparatus. Ensure that your input files conform to the expected formats for optimal results.
