# Text Editor Pseudocode

```text
PROGRAM TextEditor

CLASS TextEditor (extends JFrame)
  ATTRIBUTES: currentFile = null, findIndex = 0, debouncer, isHighlighting = false
  CONSTANTS: JAVA_KEYWORDS (list of Java reserved words)
             KEYWORD_PATTERN (regex built by joining all keywords)

MAIN
  Set look-and-feel to Nimbus (if installed)
  Create TextEditor window and show it

CONSTRUCTOR TextEditor()
  Build the GUI (menu bar, text pane, scroll pane, word-count label)
  Set up syntax highlighting listener
  Set title "Group 5 Text Editor", centre window on screen
  Create debouncer timer (400 ms, runs validateSyntax once, no repeat)
  Set layout to BorderLayout
  Build toolbar (Bold, font size, alignment buttons)
  Add text area to CENTER, word-count label to SOUTH

OPEN FILE
  Show file chooser (filter: .txt)
  IF user selects a file THEN
    TRY
      Read file contents into the text pane
      currentFile = file
      Set title to file name
    CATCH IOException
      Show error message
  END IF

NEW FILE
  Clear text pane
  currentFile = null
  Set title "Untitled"

SAVE FILE
  IF currentFile is null THEN
    Run SAVE FILE AS
  ELSE
    saveToFile(currentFile)
  END IF

SAVE FILE AS
  Show save dialog (filter: .txt)
  IF user selects a file THEN
    IF file name does not end with ".txt" THEN
      Add ".txt" to the file name
    END IF
    saveToFile(file)
  END IF

saveToFile(file)
  TRY
    Write text pane contents to file
    currentFile = file
    Set title to file name
  CATCH IOException
    Show error message

CLOSE EDITOR
  Exit program

updateMyWordCount()
  text = text pane contents, trimmed
  IF text is empty THEN
    count = 0
  ELSE
    count = number of pieces after splitting on whitespace
  END IF
  Show "Word count: " + count in label

FIND AND REPLACE
  Open dialog with: Find field, Replace field, buttons (Find Next, Replace, Replace All)

  ON Find Next
    IF find field is empty THEN stop
    findIndex = position of search text in content, starting from findIndex
    IF found THEN
      Select the match
      findIndex = end of match
    ELSE
      Show "No more matches found"
      findIndex = 0
    END IF

  ON Replace
    IF some text is selected THEN
      Replace selection with replace field text
      Run Find Next
    ELSE
      Show "Select or find a word to replace first"
    END IF

  ON Replace All
    IF find field is not empty THEN
      Replace every occurrence of find text with replace text
      Show "All occurrences replaced"
    END IF

SYNTAX HIGHLIGHTING (document listener)
  ON text inserted or removed
    updateMyWordCount()
    handleTextChange():
      IF text is empty THEN
        Stop debouncer
      ELSE
        firstWord = first word in the text
        IF firstWord is a Java keyword THEN
          Restart debouncer
          applyHighlighting()
        ELSE
          Stop debouncer
        END IF
      END IF

applyHighlighting()
  IF document is empty THEN stop
  Find the line the caret is on
  Colour the whole line black
  FOR each keyword match in the line
    Colour it blue
  END FOR

validateSyntax()  (runs when the debouncer fires)
  IF document is empty THEN stop
  Get all text as source code
  IF no Java compiler is available THEN
    Print "JDK required"
    Stop
  END IF
  Compile the text in memory (parse only), collecting errors
  Remove old highlights
  FOR each error
    IF error has a position THEN
      Highlight that range in pink
    END IF
  END FOR

TOOLBAR
  ON Bold toggled
    Switch button colours (black/white)
    Apply bold on or off to selected text
  ON font size changed
    Apply new size (10-20) to selected text
  ON Left / Center / Right clicked
    Set paragraph alignment
  ON caret moved
    Read formatting at caret
    Update Bold button state and font size spinner to match

END PROGRAM
```
