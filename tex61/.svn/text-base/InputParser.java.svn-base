package tex61;

import java.util.Scanner;
import java.util.regex.Pattern;
import java.util.regex.MatchResult;

import java.io.Reader;

import static tex61.FormatException.reportError;

/** Reads commands and text from an input source and send the results
 *  to a designated Controller. This essentially breaks the input down
 *  into "tokens"---commands and pieces of text.
 *  @author Christian Le
 */
class InputParser {

    /** Matches text between { } in a command, including the last
     *  }, but not the opening {.  When matched, group 1 is the matched
     *  text.  Always matches at least one character against a non-empty
     *  string or input source. If it matches and group 1 is null, the
     *  argument was not well-formed (the final } was missing or the
     *  argument list was nested too deeply). */
    private static final Pattern BALANCED_TEXT =
        Pattern.compile("(?s)((?:\\\\.|[^\\\\{}]"
                        + "|[{](?:\\\\.|[^\\\\{}])*[}])*)"
                        + "\\}"
                        + "|.");

    /** "Matches input to the text formatter.  Always matches something
     *  in a non-empty string or input source.  After matching, one or
     *  more of the groups described by *_TOKEN declarations will
     *  be non-null.  See these declarations for descriptions of what
     *  this pattern matches.  To test whether .group(*_TOKEN) is null
     *  quickly, check for .end(*_TOKEN) > -1).  */
    private static final Pattern INPUT_PATTERN =
        Pattern.compile("(?s)(\\p{Blank}+)"
                        + "|(\\r?\\n((?:\\r?\\n)+)?)"
                        + "|\\\\([\\p{Blank}{}\\\\])"
                        + "|\\\\(\\p{Alpha}+)([{]?)"
                        + "|((?:[^\\p{Blank}\\r\\n\\\\{}]+))"
                        + "|(.)");

    /** Symbolic names for the groups in INPUT_PATTERN. */
    private static final int
        /** Blank or tab. */
        BLANK_TOKEN = 1,
        /** End of line or paragraph. */
        EOL_TOKEN = 2,
        /** End of paragraph (>1 newline). EOL_TOKEN group will also
         *  be present. */
        EOP_TOKEN = 3,
        /** \{, \}, \\, or \ .  .group(ESCAPED_CHAR_TOKEN) will be the
         *  character after the backslash. */
        ESCAPED_CHAR_TOKEN = 4,
        /** Command (\<alphabetic characters>).  .group(COMMAND_TOKEN)
         *  will be the characters after the backslash.  */
        COMMAND_TOKEN = 5,
        /** A '{' immediately following a command. When this group is present,
         *  .group(COMMAND_TOKEN) will also be present. */
        COMMAND_ARG_TOKEN = 6,
        /** Segment of other text (none of the above, not including
         *  any of the special characters \, {, or }). */
        TEXT_TOKEN = 7,
        /** A character that should not be here. */
        ERROR_TOKEN = 8;

    /** A new InputParser taking input from READER and sending tokens to
     *  OUT. */
    InputParser(Reader reader, Controller out) {
        _input = new Scanner(reader);
        _out = out;
    }

    /** A new InputParser whose input is TEXT and that sends tokens to
     *  OUT. */
    InputParser(String text, Controller out) {
        _input = new Scanner(text);
        _out = out;
    }

    /** Break all input source text into tokens, and send them to our
     *  output controller.  Finishes by calling .close on the controller.
     */
    void process() {
        _out.defaultHash();
        while (_input.findWithinHorizon(INPUT_PATTERN, 0) != null) {
            MatchResult mat = _input.match();
            if (mat.end(EOL_TOKEN) > -1 || mat.end(BLANK_TOKEN) > -1) {
                if (_out.checkendnote()) {
                    if (_out.ifFilling() == 0) {
                        _out.endWordNoFill(mat.end(EOL_TOKEN));
                    }
                    if (_out.ifFilling() == 1) {
                        _out.endWord();
                    }
                } else {
                    if (_out.ifFilling() == 0) {
                        _out.endWordNoFill(mat.end(EOL_TOKEN));
                    }
                    if (_out.ifFilling() == 1) {
                        _out.endWord();
                    }
                }
            }
            if (mat.end(EOP_TOKEN) > -1 || !_input.hasNextLine()) {
                _out.endthatshit();
            }
            if (mat.end(ESCAPED_CHAR_TOKEN) > -1) {
                _out.addText(mat.group(ESCAPED_CHAR_TOKEN));
            }
            if (mat.end(COMMAND_TOKEN) > -1) {
                if (mat.group(COMMAND_ARG_TOKEN).equals("")) {
                    processCommand(mat.group(5), "");
                } else {
                    _input.findWithinHorizon(BALANCED_TEXT, 0);
                    MatchResult commandarg = _input.match();
                    if (_out.checkendnote() && mat.group(5).equals("endnote")) {
                        throw FormatException.error("error: %s",
                                        "endnote within endnote");
                    }
                    processCommand(mat.group(5), commandarg.group(1));
                }
            }
            if (mat.end(TEXT_TOKEN) > -1) {
                _out.addText(mat.group(7));
            }
        }
    }

    /** Process \COMMAND{ARG}. Call the appropriate methods in
     *  our Controller (_out). */
    private void processCommand(String command, String arg) {
        try {
            switch (command) {
            case "indent":
                if (Integer.parseInt(arg) >= 0) {
                    _out.setIndentation(Integer.parseInt(arg));
                }
                if (Integer.parseInt(arg) < 0) {
                    throw FormatException.error("error: %s",
                                        "indent cannot be negative");
                }
                break;
            case "parindent":
                _out.setParIndentation(Integer.parseInt(arg));
                break;
            case "endnote":
                setEndnote(arg);
                break;
            case "textwidth":
                if (Integer.parseInt(arg) >= 0) {
                    _out.setTextWidth(Integer.parseInt(arg));
                }
                if (Integer.parseInt(arg) < 0) {
                    throw FormatException.error("error: %s",
                                        "textwidth cannot be negative");
                }
                break;
            case "textheight":
                settextheigh(arg);
                break;
            case "parskip":
                setparagraphskip(arg);
                break;
            case "nofill":
                _out.setFill(0);
                _out.setJustify(0);
                break;
            case "fill":
                _out.setFill(1);
                break;
            case "justify":
                _out.setJustify(1);
                break;
            case "nojustify":
                _out.setJustify(0);
                break;
            default:
                reportError("unknown command: %s", command);
                break;
            }
        } catch (FormatException e) {
            reportError(e.getMessage());
            System.exit(1);
        }
    }

    /** Sets the paragraphskip to ARG. */
    void setparagraphskip(String arg) {
        if (Integer.parseInt(arg) >= 0) {
            _out.setParSkip(Integer.parseInt(arg));
        }
        if (Integer.parseInt(arg) < 0) {
            throw FormatException.error("error: %s",
                "textheight cannot be negative");
        }
    }

    /** Set text height to ARG. */
    void settextheigh(String arg) {
        if (Integer.parseInt(arg) >= 0) {
            _out.setTextHeight(Integer.parseInt(arg));
        }
        if (Integer.parseInt(arg) < 0) {
            throw FormatException.error("error: %s",
                "textheight cannot be negative");
        }
    }

    /** Deal with endnote command. Store ENDNOTE in endnoteStore. */
    void setEndnote(String endnote) {
        _out.incrementRefNum();
        _out.addRefNum();
        _out.storeEndnoteString(endnote + " ");
    }

    /** My input source. */
    private final Scanner _input;
    /** The Controller to which I send input tokens. */
    private Controller _out;

}
