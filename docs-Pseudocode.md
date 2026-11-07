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
  Set title "Group 5 Text Editor - Untitled", centre window on screen
  Create debouncer timer (400 ms, runs validateSyntax once, no repeat)
  Set layout to BorderLayout
  Build toolbar (Bold, font size spinner 10-20, alignment buttons)
  Add toolbar to NORTH, text area to CENTER, word-count label to SOUTH

OPEN FILE
  Show file chooser (filter: .txt)
  IF user selects a file THEN
    TRY
      Read file contents into the text pane
      currentFile = file
      Set title to file name
      
      text = text pane contents with leading whitespace removed
      IF text is not empty THEN
        firstWord = first word in text, stripped of non-letters
        IF firstWord matches KEYWORD_PATTERN THEN
          applyFullDocumentHighlighting()
          validateSyntax()
        END IF
      END IF
    CATCH IOException
      Show error message "Error reading file"
  END IF

NEW FILE
  Clear text pane
  currentFile = null
  Set title "Text Editor - Untitled"

SAVE FILE
  IF currentFile is null THEN
    Run SAVE FILE AS
  ELSE
    saveToFile(currentFile)
  END IF

SAVE FILE AS
  Show save dialog (filter: .txt)
  IF user selects a file THEN
    IF file path does not end with ".txt" (case-insensitive) THEN
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
    Show error message "Error saving file"

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
    content = text pane contents
    findIndex = position of find field text in content, starting from findIndex
    IF found THEN
      Focus text pane and select the match
      findIndex = end of match
    ELSE
      Show "No more matches found"
      findIndex = 0
    END IF

  ON Replace
    selectedText = selected text from pane
    IF selectedText is not null and not empty THEN
      Replace selection with replace field text
      Run Find Next
    ELSE
      Show "Select or find a word to replace first"
    END IF

  ON Replace All
    IF find field is not empty THEN
      content = text pane contents
      newContent = replace all occurrences of find field text with replace field text
      Set text pane contents to newContent
      Show "All occurrences replaced"
    END IF

SYNTAX HIGHLIGHTING (document listener)
  ON text inserted or removed
    updateMyWordCount()
    handleTextChange() async on UI thread:
      text = text pane contents with leading whitespace removed
      IF text is empty THEN
        Stop debouncer
        stop
      END IF
      
      IF text starts with "/*" THEN
        commentEnd = position of "*/"
        IF commentEnd is found THEN
          text = remaining text after "*/" with leading whitespace removed
        END IF
      END IF
      
      firstWord = first piece of text split by whitespace, stripped of non-letters
      IF firstWord matches KEYWORD_PATTERN THEN
        Restart debouncer
        applyHighlighting() async on UI thread
      ELSE
        Stop debouncer
      END IF

applyHighlighting()
  IF isHighlighting is true THEN stop
  IF document is empty THEN stop
  
  caretPos = position of caret
  isHighlighting = true
  
  Find the line the caret is on
  Colour the whole current line black
  
  FOR each keyword match in the current line
    Colour match range blue
  END FOR
  
  isHighlighting = false

applyFullDocumentHighlighting()
  IF document is empty THEN stop
  isHighlighting = true
  
  Colour entire text document black
  text = all text in document
  
  FOR each keyword match in text
    Colour match range blue
  END FOR
  
  isHighlighting = false

validateSyntax() (runs when the debouncer fires)
  IF document is empty THEN stop
  sourceCode = get all text from document
  
  IF no Java compiler is available from system THEN
    Print "JDK required"
    Stop
  END IF
  
  Compile the sourceCode in memory (parse parameters only), collecting diagnostics
  Remove all old error highlights from text pane
  
  FOR each diagnostic error
    startPos = error start position
    endPos = error end position
    
    IF startPos is valid THEN
      IF endPos is invalid or less than/equal to startPos THEN
        endPos = startPos + 1
      END IF
      Highlight range from startPos to endPos in pink
    END IF
  END FOR

TOOLBAR
  ON Bold toggled
    IF button is selected THEN
      Set button background black, text white
    ELSE
      Set button background white, text black
    END IF
    Apply bold layout to selected text character attributes
    Refocus text pane

  ON font size changed
    newSize = value from spinner
    Apply new size to selected text character attributes
    Refocus text pane

  ON Left clicked
    Set current paragraph alignment to ALIGN_LEFT
    Refocus text pane

  ON Center clicked
    Set current paragraph alignment to ALIGN_CENTER
    Refocus text pane

  ON Right clicked
    Set current paragraph alignment to ALIGN_RIGHT
    Refocus text pane

  ON caret moved
    IF document length > 0 THEN
      attrPos = if caret position > 0 then caret position - 1 else 0
      attrs = get character attributes at attrPos
      
      isBold = check if bold is active in attrs
      Set Bold button selection state to isBold
      Set Bold button colours based on isBold (black/white or white/black)
      
      fontSize = get font size from attrs
      IF fontSize between 10 and 20 THEN
        Set font size spinner value to fontSize
      END IF
    END IF

END PROGRAM
```
