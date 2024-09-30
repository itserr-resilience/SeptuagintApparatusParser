# EBNF Structure of the Septuagint Apparatus

This document presents the Extended Backus-Naur Form (EBNF) grammar that defines the structure of the Septuagint apparatus. 
The parser processes textual variants and readings from the Septuagint Bible and converts them into a structured format. 
This EBNF aims to capture the syntactic rules as implemented in the parser code, with a particular focus on the **witness identifiers (`<witness_id>`)** and how they are processed within the apparatus.

---

## EBNF Grammar (Updated)

```ebnf
<apparatus>          ::= { <entry_line> }

<entry_line>         ::= [ <chapter_verse> ] <entry_content>

<chapter_verse>      ::= [ <number> ":" ] <number>

<entry_content>      ::= <variant> { " | " <variant> }

<variant>            ::= [ <location> "]" ] <readings>

<location>           ::= <compound_location> [ " " <witnesses> ]

<compound_location>  ::= <single_location> { "/" <single_location> }

<single_location>    ::= <range>

<range>              ::= <loc> [ <range_indicator> <loc> ]

<range_indicator>    ::= "—" | "…" | "-"

<loc>                ::= [ "(" <number> ")" ] <loc_value>

<loc_value>          ::= "fin" | "init" | "comma" | <text_location>

<text_location>      ::= <greek_text> [ <number> "°" ] | <greek_text> "ult"

<readings>           ::= <reading> { ";" <reading> }

<reading>            ::= [ <cause_type> ] [ <greek_text> ] [ <witnesses> ] [ <note> ]

<cause_type>         ::= <abbreviation> { <abbreviation> }

<abbreviation>       ::= [A-Za-z]+

<witnesses>          ::= <witness> { [ " " | "-" ] <witness> }

<witness>            ::= <witness_id> [ <note> ]

<witness_id>         ::= "𝔐"
                      | "Lat" <alphanum_sequence>
                      | <digit_sequence> [ <non_alphanum_sequence> ]
                      | <letter_sequence> [ <digit_sequence> ] [ <non_alphanum_sequence> ]
                      | <alphanum_sequence> [ <non_alphanum_sequence> ]

<note>               ::= "(" <note_content> ")"

<note_content>       ::= { <any_char_except_closing_paren> }

<number>             ::= [0-9]+

<greek_text>         ::= { <greek_char> | "." | "ʼ" | "̅" | "-" | " " }

<any_char_except_closing_paren> ::= any character except ")"

<greek_char>         ::= any Unicode character in Greek ranges or specific punctuation

<alphanum_sequence>  ::= ( <letter> | <digit> ) { <letter> | <digit> }

<digit_sequence>     ::= <digit> { <digit> }

<letter_sequence>    ::= <letter> { <letter> }

<non_alphanum_sequence> ::= { <non_alphanum_char> }

<letter>             ::= [A-Za-z] | Greek letters

<digit>              ::= [0-9]

<non_alphanum_char>  ::= any character except letters and digits
```

---

## Detailed Description (Updated)

### Apparatus Structure

#### `<apparatus>`

- Represents the entire apparatus, which consists of multiple entry lines.
- **Definition**: A sequence of `<entry_line>` elements.

#### `<entry_line>`

- Corresponds to a single line in the apparatus file.
- **Definition**: Optionally starts with `<chapter_verse>`, followed by `<entry_content>`.

#### `<chapter_verse>`

- Specifies the chapter and verse numbers.
- **Definition**: Optionally includes a chapter `<number>` followed by a colon and a verse `<number>`, or just a verse `<number>`.
- **Examples**:
  - `"3:16"` (Chapter 3, Verse 16)
  - `"16"` (Verse 16, chapter inferred from context)

### Entry Content

#### `<entry_content>`

- Contains one or more `<variant>` elements, separated by `" | "`.
- **Definition**: A `<variant>` followed by zero or more sequences of `" | "` and another `<variant>`.

#### `<variant>`

- Represents a textual variant in the apparatus.
- **Definition**: Optionally starts with a `<location>` and a closing bracket `"]"`, followed by `<readings>`.

### Location Parsing

#### `<location>`

- Specifies where in the text the variant occurs.
- **Definition**: A `<compound_location>`, optionally followed by witnesses.

#### `<compound_location>`

- Handles multiple locations separated by `"/"`.
- **Definition**: A `<single_location>` followed by zero or more sequences of `"/"` and another `<single_location>`.

#### `<single_location>`

- Represents a single location, which may include a range.
- **Definition**: A `<range>`.

#### `<range>`

- Captures a location range using range indicators.
- **Definition**: A starting `<loc>`, optionally followed by a `<range_indicator>` and an ending `<loc>`.

- **Range Indicators**:
  - `"—"` (Em dash)
  - `"…"` (Ellipsis)
  - `"-"` (Hyphen)

- **Processing**:
  - If a range indicator is present, the parser treats the two `<loc>` elements as the start and end of the range.
  - This is particularly important for accurately identifying the span of text affected by the variant.

#### `<loc>`

- Represents a specific location within the text.
- **Definition**: Optionally includes a verse ID in parentheses, followed by a `<loc_value>`.

- **Examples**:
  - `"(16) fin"` (Verse 16, at the end)
  - `"init"` (At the beginning)
  - `"λόγος 2°"` (The second occurrence of the word "λόγος")

#### `<loc_value>`

- Specifies the type of location.
- **Possible Values**:
  - `"fin"`: Indicates the end of a section.
  - `"init"`: Indicates the beginning of a section.
  - `"comma"`: Indicates a comma location.
  - `<text_location>`: Specifies an exact textual location.

#### `<text_location>`

- Provides a textual reference, often in Greek, with optional qualifiers.
- **Definition**:
  - `<greek_text>` optionally followed by a `<number>` and a degree symbol `"°"`.
  - Alternatively, `<greek_text>` followed by `"ult"` to indicate the last occurrence.

- **Examples**:
  - `"λόγος 2°"`: The second occurrence of "λόγος".
  - `"λόγος ult"`: The last occurrence of "λόγος".

### Readings

#### `<readings>`

- Contains one or more `<reading>` elements, separated by `";"`.
- **Definition**: A `<reading>` followed by zero or more sequences of `";"` and another `<reading>`.

#### `<reading>`

- Represents an individual reading or variant.
- **Definition**: Optionally includes `<cause_type>`, `<greek_text>`, `<witnesses>`, and a `<note>`.

- **Components**:
  - `<cause_type>`: Abbreviations indicating the type or cause of the variant.
  - `<greek_text>`: The variant text in Greek.
  - `<witnesses>`: Sources that support the reading.
  - `<note>`: Additional information enclosed in parentheses.

#### `<cause_type>`

- Consists of abbreviations that describe the variant.
- **Definition**: One or more `<abbreviation>` elements.

- **Examples**:
  - `"tr"`: Transposition.
  - `"om"`: Omission.

#### `<abbreviation>`

- An abbreviation representing a cause or type.
- **Definition**: One or more alphabetic characters.

### Witnesses (Detailed)

#### `<witnesses>`

- Lists the witnesses (manuscripts, versions, etc.) that attest to the reading.
- **Definition**: A `<witness>` followed by zero or more sequences of a space or hyphen and another `<witness>`.

#### `<witness>`

- Represents an individual witness.
- **Definition**: A `<witness_id>`, optionally followed by a `<note>`.

#### `<witness_id>` (Detailed)

The witness identifiers can have various forms, reflecting the diversity in manuscript notation. The parser code handles several special cases, which are captured in the updated EBNF.

- **Definition**:
  - **Special Identifiers**:
    - `"𝔐"`: Represents the Majority Text.
    - `"Lat"` `<alphanum_sequence>`: Latin manuscripts or versions, where `"Lat"` is followed by an alphanumeric sequence.
  - **Numeric Identifiers**:
    - `<digit_sequence>`: One or more digits, possibly followed by non-alphanumeric characters (e.g., `"120*"`, `"120a"`).
  - **Alphabetic Identifiers**:
    - `<letter_sequence>`: One or more letters, possibly followed by digits and non-alphanumeric characters (e.g., `"A"`, `"B2"`).
  - **Alphanumeric Identifiers**:
    - `<alphanum_sequence>`: A combination of letters and digits (e.g., `"P45"`, `"f1"`).
  - **Non-Alphanumeric Suffixes**:
    - `<non_alphanum_sequence>`: Characters such as asterisks, hyphens, or other symbols (e.g., `"A*"`, `"120*"`, `"B'"`).

- **Components**:
  - `<digit_sequence>`: One or more digits `[0-9]+`.
  - `<letter_sequence>`: One or more letters `[A-Za-z]+`, including Greek letters.
  - `<alphanum_sequence>`: A mix of letters and digits.
  - `<non_alphanum_sequence>`: Characters that are neither letters nor digits, such as `'*'`, `'–'`, or punctuation.

- **Examples**:
  - `"𝔐"`: Special identifier for the Majority Text.
  - `"LatV"`: Latin version `V`.
  - `"120"`: Manuscript numbered `120`.
  - `"A"`: Manuscript labeled `A`.
  - `"P45"`: Papyrus `45`.
  - `"f1"`: Family `1`.
  - `"120*"`: Manuscript `120` with an asterisk, indicating a particular notation.
  - `"B'"`: Manuscript `B` with a prime symbol.

**Special Cases Handled in the Parser Code**:

- **Single Greek Character Witnesses**: Witnesses like `"𝔐"` are recognized directly.
- **Witnesses Starting with Digits**:
  - Segments starting with digits are treated as numeric witness IDs.
  - Non-digit suffixes may be considered notes or part of the witness ID.
- **Witnesses with Uppercase and Lowercase Letters**:
  - Two-letter identifiers where the first is uppercase and the second is lowercase (e.g., `"Pc"`) may represent a witness with a note.
- **Witnesses Starting with `"Lat"`**:
  - `"Lat"` followed by letters or numbers denotes Latin manuscripts or versions.
- **Witnesses with Hyphens or Special Characters**:
  - Witness IDs may contain hyphens or other non-alphanumeric characters, which are handled appropriately.
- **Combined Segments**:
  - In cases where a witness ID spans multiple segments (e.g., `"Lib geneal"`), the parser attempts to combine them.

### Notes

#### `<note>`

- Provides additional information about a witness or reading.
- **Definition**: Text enclosed in parentheses.

- **Examples**:
  - `"(lac)"`: Indicates a lacuna (gap) in the manuscript.
  - `"(vid)"`: Indicates that the reading is uncertain.

### Lexical Elements

#### `<number>`

- A sequence of digits.
- **Definition**: One or more digits `[0-9]+`.

#### `<greek_text>`

- A sequence of Greek characters and specific punctuation.
- **Definition**: Zero or more `<greek_char>` or allowed punctuation.

- **Allowed Characters**:
  - Greek Unicode characters.
  - Punctuation: `"."`, `"ʼ"`, `"̅"`, `"-"`, and space.

#### `<greek_char>`

- Any Unicode character in the Greek script ranges.
- **Unicode Ranges**:
  - `\u0370` to `\u03FF`
  - `\u1F00` to `\u1FFF`

#### `<any_char_except_closing_paren>`

- Any character except the closing parenthesis `")"`.

#### `<alphanum_sequence>`

- A sequence of letters and digits.
- **Definition**: A letter or digit followed by zero or more letters or digits.

#### `<letter_sequence>`

- A sequence of letters.
- **Definition**: One or more letters.

#### `<digit_sequence>`

- A sequence of digits.
- **Definition**: One or more digits.

#### `<non_alphanum_sequence>`

- A sequence of non-alphanumeric characters.
- **Definition**: Zero or more characters that are neither letters nor digits.

#### `<letter>`

- An alphabetic character.
- **Definition**: Uppercase or lowercase letters `[A-Za-z]`, including Greek letters.

#### `<digit>`

- A numeric digit.
- **Definition**: One of `[0-9]`.

#### `<non_alphanum_char>`

- Any character except letters and digits.
- **Examples**: Symbols like `'*'`, `'–'`, `'.'`, `'"'`, etc.

---

## Special Considerations

### Witness Identifier Parsing

- **Complexity**: Witness identifiers can be complex, involving combinations of letters, digits, and special characters.
- **Parser Logic**:
  - **Special Cases**: The parser recognizes specific identifiers like `"𝔐"` and prefixes like `"Lat"`.
  - **Digits and Letters**: Witness IDs may start with digits (e.g., `"120"`) or letters (e.g., `"A"`, `"B2"`).
  - **Non-Alphanumeric Suffixes**: Characters like `'*'` or `'–'` may be attached to witness IDs.
  - **Combined Segments**: The parser attempts to combine adjacent segments if they likely form a single witness ID (e.g., `"Lib geneal"`).
- **Handling Notes**:
  - Non-alphanumeric suffixes or additional characters may be treated as notes associated with the witness.
  - Parenthetical notes are explicitly parsed and attached to the witness.

### Range Element in Location

- **Importance**: The range element allows specifying a span of text rather than a single point.
- **Syntax**: `<loc> [ <range_indicator> <loc> ]`
  - If the range indicator is present, it signifies that the variant affects all text between the two `<loc>` positions.
- **Processing in Parser**:
  - The parser searches for range indicators (`"—"`, `"…"`, `"-"`) to identify ranges.
  - Start and end locations are extracted and structured using the Web Annotation Ontology (`oa:RangeSelector`).
  - If no range is detected, the location is treated as a single point.

### Witness Processing

- Witnesses can have notes and may require complex parsing to extract information accurately.
- The parser handles special cases where witness IDs might be combined with notes or other qualifiers.
- Witness information is essential for textual criticism and understanding the attestation of variants.

---

## Examples

1. **Single Witness with Special Identifier**:

   ```
   3:16 λόγος] 𝔐
   ```

   - **Chapter and Verse**: `3:16`
   - **Location**: `λόγος`
   - **Witnesses**: `𝔐` (Majority Text)

2. **Witness Starting with Digits and Non-Alphanumeric Suffix**:

   ```
   5:12 λόγος] 120* (lac)
   ```

   - **Chapter and Verse**: `5:12`
   - **Location**: `λόγος`
   - **Witness**: `120*` with a note `(lac)`

3. **Witness Starting with 'Lat'**:

   ```
   7:8 λόγος] LatV
   ```

   - **Chapter and Verse**: `7:8`
   - **Location**: `λόγος`
   - **Witness**: `LatV` (Latin Version `V`)

4. **Witness with Hyphen and Note**:

   ```
   9:10 λόγος] A- (vid)
   ```

   - **Chapter and Verse**: `9:10`
   - **Location**: `λόγος`
   - **Witness**: `A-` with a note `(vid)`

5. **Combined Witness Segments**:

   ```
   12:5 λόγος] Lib geneal
   ```

   - **Chapter and Verse**: `12:5`
   - **Location**: `λόγος`
   - **Witness**: `Lib geneal` (combined from two segments)

6. **Witness with Uppercase and Lowercase Letters**:

   ```
   15:3 λόγος] Pc (corr)
   ```

   - **Chapter and Verse**: `15:3`
   - **Location**: `λόγος`
   - **Witness**: `Pc` with a note `(corr)`
