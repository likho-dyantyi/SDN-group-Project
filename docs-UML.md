```mermaid
classDiagram
    class TextEditor {
        -Logger logger
        -File currentFile
        -FileController fileController
        -SyntaxController syntaxController
        -JToolBar jToolBar1
        -JToggleButton btnBold
        -JSpinner spnFontSize
        -JButton btnAlignLeft
        -JButton btnAlignCenter
        -JButton btnAlignRight
        -JTextPane jtpWordSpace
        -JLabel jlblWords
        +TextEditor()
        +getWordSpace() JTextPane
        +setWordCountText(String text) void
        +getCurrentFile() File
        +setCurrentFile(File file) void
        +getSyntaxController() SyntaxController
        +main(String[] args) static void
        -initComponents() void
        -setupToolbarAndFormatting() void
    }

    class FileController {
        -TextEditor view
        +FileController(TextEditor view)
        +openFile() void
        +saveFile(boolean saveAs) void
        -executeSave(File file) void
    }

    class SyntaxController {
        -TextEditor view
        -Timer debouncer
        -boolean isHighlighting
        -String[] JAVA_KEYWORDS static
        -Pattern KEYWORD_PATTERN static
        +SyntaxController(TextEditor view)
        +getKeywordPattern() static Pattern
        +updateMyWordCount() void
        +validateSyntax() void
        +applyFullDocumentHighlighting() void
        -initSyntaxHighlighting() void
        -applyHighlighting() void
    }

    class FindReplaceDialog {
        -JTextField findField
        -JTextField replaceField
        -JButton findButton
        -JButton replaceButton
        -JButton replaceAllButton
        -JTextPane editorPane
        -int findIndex
        +FindReplaceDialog(JFrame parent, JTextPane editorPane)
        -setupActions() void
    }

    TextEditor "1" *-- "1" FileController : manages file operations
    TextEditor "1" *-- "1" SyntaxController : manages parsing & styling
    TextEditor ..> FindReplaceDialog : instantiates on menu click
    FileController --> TextEditor : updates state / reads text
    SyntaxController --> TextEditor : updates state / highlights text
    FindReplaceDialog --> JTextPane : manipulates target text space
