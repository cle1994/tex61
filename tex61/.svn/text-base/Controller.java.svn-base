package tex61;

import java.io.PrintWriter;
import java.util.ArrayList;
import java.util.HashMap;

/** Receives (partial) words and commands, performs commands, and
 *  accumulates and formats words into lines of text, which are sent to a
 *  designated PageAssembler.  At any given time, a Controller has a
 *  current word, which may be added to by addText, a current list of
 *  words that are being accumulated into a line of text, and a list of
 *  lines of endnotes.
 *  @author Christian Le
 */
class Controller {

    /** Initiate HashMap of commands. */
    private HashMap<String, Integer> _demCommands =
                                        new HashMap<String, Integer>();
    /** Initiate ArrayList of normal text. */
    private ArrayList<String> _mainText = new ArrayList<String>();
    /** Initiate ArrayList of text for endnotes. */
    private ArrayList<String> _endnoteText = new ArrayList<String>();
    /** Initiate an ArrayList of text for storing endnotes. */
    private ArrayList<String> _endnoteStore = new ArrayList<String>();
    /** An arraylist of formatted maintext. */
    private ArrayList<String> _mainTextStore = new ArrayList<String>();

    /** A new Controller that sends formatted output to OUT. */
    Controller(PrintWriter out) {
        printer = new PagePrinter(out);
        line = new LineAssembler(printer);
        _refNum = 0;
        _unfinished = "";
        l = 0;
        lEnd = 0;
        pageline = 0;
    }

    /** Returns whether or not endnote. */
    boolean checkendnote() {
        return _endnoteMode;
    }

    /** Store ENDNOTE string to later build endnotes. */
    void storeEndnoteString(String endnote) {
        _endnoteStore.add(endnote);
    }

    /** Return endnoteStore length. */
    int endnoteStoreLength() {
        return _endnoteStore.size();
    }

    /** Return given string in array at given INDEX. */
    String endnoteString(int index) {
        return _endnoteStore.get(index);
    }

    /** Add TEXT to the end of the word of formatted text currently
     *  being accumulated. */
    void addText(String text) {
        _unfinished += text;
    }

    /** Add _refNum to current word. */
    void addRefNum() {
        _unfinished += "[" + Integer.toString(_refNum) + "]";
    }

    /** Takes the INDEX from _endnoteStore and adds 1 to set corrent _refNum. */
    void refNumSetter(int index) {
        _refNum = index + 1;
    }

    /** Inserts the correct number of spaces after refnum in endnotes. Returns
     *  String fo spaces after a reference. */
    String spaceAfterRef() {
        String spaces = "";
        String refnum = Integer.toString(_refNum);
        int length = _demCommands.get("endnoteindent")
                    - _demCommands.get("endnoteparindent")
                    - refnum.length() - 2;
        if (_endnoteMode) {
            if (length > _demCommands.get("endnoteindent")) {
                spaces = " ";
            } else {
                int i = 0;
                while (i < length) {
                    spaces += " ";
                    i += 1;
                }
            }
        } else {
            spaces = "";
        }
        return spaces;
    }

    /** Endword for dealing with no fill. Takes in ENDLINE to see when to break
     *  line */
    void endWordNoFill(int endline) {
        String finished = _unfinished;
        indentation = 0;
        if (_endnoteMode) {
            if (lineinendnote == 0) {
                indentation = _demCommands.get("endnoteindent")
                            + (_demCommands.get("endnoteparindent"));
            }
            if (lineinendnote > 0) {
                indentation = _demCommands.get("endnoteindent");
            }
        } else {
            if (lineinparagraph == 0) {
                indentation = _demCommands.get("indent")
                            + (_demCommands.get("parindent"));
            }
            if (lineinparagraph > 0) {
                indentation = _demCommands.get("indent");
            }
        }
        _unfinished = "";
        if (endline > -1) {
            if (_endnoteMode) {
                _endnoteText.add(finished);
            } else {
                _mainText.add(finished);
            }
            System.out.println(_mainText);
            String fin;
            int parskip;
            if (_endnoteMode) {
                fin = line.nofilljustifier(_endnoteText, indentation,
                                    _refNum, lineinendnote, _endnoteMode);
                parskip = _demCommands.get("endnoteparskip");
            } else {
                fin = line.nofilljustifier(_mainText, indentation,
                                    _refNum, lineinparagraph, _endnoteMode);
                parskip = _demCommands.get("parskip");
            }
            if (lineinparagraph == 0 && pageline != 0) {
                for (int i = 0; i < parskip; i += 1) {
                    String skipline = line.shortline(null, indentation,
                                    _endnoteMode, _refNum, lineinendnote,
                                    spaceAfterRef());
                    _mainTextStore.add(skipline);
                }
            }
            _mainTextStore.add(fin);
            pageline += 1;
            addNewline();
        } else {
            if (!finished.equals("")) {
                if (_endnoteMode) {
                    _endnoteText.add(finished);
                } else {
                    _mainText.add(finished);
                }
            }
        }
    }

    /** Takes in FINISHED and return maxspaces for endword(). */
    int maximumspace(String finished) {
        int maxspaces = 0;
        if (_endnoteMode) {
            if (lineinendnote == 0) {
                maxspaces = _demCommands.get("endnotetextwidth")
                            - (lEnd + finished.length())
                            - _demCommands.get("endnoteindent")
                            - _demCommands.get("endnoteparindent")
                            - Integer.toString(_refNum).length() - 2
                            - spaceAfterRef().length();
            }
            if (lineinendnote > 0) {
                maxspaces = _demCommands.get("endnotetextwidth")
                            - (lEnd + finished.length())
                            - _demCommands.get("endnoteindent");
            }
        } else {
            if (lineinparagraph == 0) {
                maxspaces = _demCommands.get("textwidth")
                            - (l + finished.length())
                            - _demCommands.get("indent")
                            - _demCommands.get("parindent");
            }
            if (lineinparagraph > 0) {
                maxspaces = _demCommands.get("textwidth")
                            - (l + finished.length())
                            - _demCommands.get("indent");
            }
        }
        return maxspaces;
    }

    /** Returns the total amount of indentation. */
    int indentationamount() {
        indentation = 0;
        if (_endnoteMode) {
            if (lineinendnote == 0) {
                indentation = _demCommands.get("endnoteindent")
                            + (_demCommands.get("endnoteparindent"));
            }
            if (lineinendnote > 0) {
                indentation = _demCommands.get("endnoteindent");
            }
        } else {
            if (lineinparagraph == 0) {
                indentation = _demCommands.get("indent")
                            + _demCommands.get("parindent");
            }
            if (lineinparagraph > 0) {
                indentation = _demCommands.get("indent");
            }
        }
        return indentation;
    }

    /** Adds WORD to pending line. */
    void addWord(String word) {
        if (_endnoteMode) {
            _endnoteText.add(word);
            lEnd += word.length();
        } else {
            _mainText.add(word);
            l += word.length();
        }
    }

    /** Returns length of pending line. */
    int textlength() {
        int length = 0;
        if (_endnoteMode) {
            length = _endnoteText.size();
        } else {
            length = _mainText.size();
        }
        return length;
    }

    /** Adds skipped lines to _mainTextStore. */
    void paragraphskipping() {
        if (_endnoteMode) {
            for (int i = 0; i < _demCommands.get("endnoteparskip"); i += 1) {
                String skipline = line.shortline(null, indentation,
                            _endnoteMode, _refNum, lineinendnote,
                            spaceAfterRef());
                _mainTextStore.add(skipline);
            }
        } else {
            for (int i = 0; i < _demCommands.get("parskip"); i += 1) {
                String skipline = line.shortline(null, indentation,
                            _endnoteMode, _refNum, lineinendnote,
                            spaceAfterRef());
                _mainTextStore.add(skipline);
            }
        }
    }

    /** Finish any current word of text and, if present, add to the
     *  list of words for the next line.  Has no effect if no unfinished
     *  word is being accumulated. */
    void endWord() {
        String finished = _unfinished;
        int textwidth = 0;
        int maxspaces = maximumspace(finished);
        int textlen = textlength();
        String fin;
        indentation = indentationamount();
        _unfinished = "";
        if (_endnoteMode) {
            if (finished.length() > _demCommands.get("endnotetextwidth")
                                    - _demCommands.get("endnoteindent")
                                    - _demCommands.get("endnoteparindent")
                                    && _endnoteText.size() == 0) {
                textwidth = finished.length() + indentation;
                maxspaces = 0;
            } else {
                textwidth = _demCommands.get("endnotetextwidth");
            }
        } else {
            if (finished.length() > _demCommands.get("textwidth")
                                    - _demCommands.get("indent")
                                    - _demCommands.get("parindent")
                                    && _mainText.size() == 0) {
                textwidth = finished.length() + indentation;
                maxspaces = 0;
            } else {
                textwidth = _demCommands.get("textwidth");
            }
        }
        line.getRefNum(_refNum);
        line.getEndnoteMode(_endnoteMode);
        if (maxspaces < textlen) {
            if (_endnoteMode) {
                fin = line.justifier(_endnoteText, textwidth - indentation,
                        lEnd, _endnoteText.size(), indentation, lineinendnote,
                        _demCommands.get("endnotejustify"), spaceAfterRef());
                if (lineinendnote == 0 && pageline != 0) {
                    paragraphskipping();
                }
            } else {
                fin = line.justifier(_mainText, textwidth - indentation,
                        l, _mainText.size(), indentation, lineinendnote,
                        _demCommands.get("justify"), spaceAfterRef());
                if (lineinparagraph == 0 && pageline != 0) {
                    paragraphskipping();
                }
            }
            _mainTextStore.add(fin);
            pageline += 1;
            addNewline();
            addWord(finished);
        } else {
            if (!finished.equals("")) {
                addWord(finished);
            }
        }
    }

    /** Function that ends paragraph. */
    void endthatshit() {
        String fin;
        int parskip;
        indentation = indentationamount();
        if (_endnoteMode) {
            fin = line.shortline(_endnoteText, indentation, _endnoteMode,
                                _refNum, lineinendnote, spaceAfterRef());
            parskip = _demCommands.get("endnoteparskip");
        } else {
            fin = line.shortline(_mainText, indentation, _endnoteMode,
                                _refNum, lineinendnote, spaceAfterRef());
            parskip = _demCommands.get("parskip");
        }
        if (lineinparagraph == 0 && pageline != 0) {
            for (int i = 0; i < parskip; i += 1) {
                String skipline = line.shortline(null, indentation,
                            _endnoteMode, _refNum, lineinendnote,
                            spaceAfterRef());
                _mainTextStore.add(skipline);
            }
        }
        _mainTextStore.add(fin);
        pageline += 1;
        addnewParagraph();
    }

    /** Finishe any current word of formatted text and process a new
     *  paragraph. */
    void addnewParagraph() {
        if (_endnoteMode) {
            lEnd = 0;
            lineinendnote = 0;
            _endnoteText.clear();
        } else {
            l = 0;
            lineinparagraph = 0;
            _mainText.clear();
        }
    }

    /** Finish any current word of formatted text and process an end-of-line
     *  according to the current formatting parameters. */
    void addNewline() {
        if (_endnoteMode) {
            lineinendnote += 1;
            lEnd = 0;
            _endnoteText.clear();
        } else {
            lineinparagraph += 1;
            l = 0;
            _mainText.clear();
        }
    }

    /** Set the current text height (number of lines per page) to VAL, if
     *  it is a valid setting.  Ignored when accumulating an endnote. */
    void setTextHeight(int val) {
        _demCommands.put("textheight", val);
    }

    /** Set the current text width (width of lines including indentation)
     *  to VAL, if it is a valid setting. */
    void setTextWidth(int val) {
        if (_endnoteMode) {
            _demCommands.put("endnotetextwidth", val);
        } else {
            _demCommands.put("textwidth", val);
        }
    }

    /** Set the current text indentation (number of spaces inserted before
     *  each line of formatted text) to VAL, if it is a valid setting. */
    void setIndentation(int val) {
        if (_endnoteMode) {
            _demCommands.put("endnoteindent", val);
        } else {
            _demCommands.put("indent", val);
        }
    }

    /** Set the current paragraph indentation (number of spaces inserted before
     *  first line of a paragraph in addition to indentation) to VAL, if it is
     *  a valid setting. */
    void setParIndentation(int val) {
        if (_endnoteMode) {
            _demCommands.put("endnoteparindent", val);
        } else {
            _demCommands.put("parindent", val);
        }
    }

    /** Set the current paragraph skip (number of blank lines inserted before
     *  a new paragraph, if it is not the first on a page) to VAL, if it is
     *  a valid setting. */
    void setParSkip(int val) {
        if (_endnoteMode) {
            _demCommands.put("endnoteparskip", val);
        } else {
            _demCommands.put("parskip", val);
        }
    }

    /** Iff ON, begin filling lines of formatted text. */
    void setFill(int on) {
        if (_endnoteMode) {
            _demCommands.put("endnotefill", on);
        } else {
            _demCommands.put("fill", on);
        }
    }

    /** Returns whether setfill is on or off. */
    int ifFilling() {
        if (_endnoteMode) {
            return _demCommands.get("endnotefill");
        } else {
            return _demCommands.get("fill");
        }
    }

    /** Iff ON, begin justifying lines of formatted text whenever filling is
     *  also on. */
    void setJustify(int on) {
        if (_endnoteMode) {
            _demCommands.put("endnotejustify", on);
        } else {
            _demCommands.put("justify", on);
        }
    }

    /** Finish the current formatted document or endnote (depending on mode).
     *  Formats and outputs all pending text. */
    void close() {
        if (_endnoteMode) {
            endthatshit();
            setNormalMode();
        } else {
            endthatshit();
            sendToPrinter();
        }
    }

    /** Once all text is compiled, sendToPrinter() prints text. */
    void sendToPrinter() {
        int printingpageline = 0;
        for (int i = 0; i < _mainTextStore.size(); i += 1) {
            if (printingpageline == _demCommands.get("textheight")) {
                if (_mainTextStore.get(i) == null) {
                    i = i;
                } else {
                    printer.write("\f" + _mainTextStore.get(i));
                    printingpageline = 0;
                }
            }
            if (printingpageline != _demCommands.get("textheight")) {
                if (_mainTextStore.get(i).equals("")) {
                    printer.write("");
                    printingpageline += 1;
                } else {
                    printer.write(_mainTextStore.get(i));
                    printingpageline += 1;
                }
            }
        }
    }

    /** Sets all the default command values. */
    void defaultHash() {
        _demCommands.put("textheight", Defaults.TEXT_HEIGHT);
        _demCommands.put("parskip", Defaults.PARAGRAPH_SKIP);
        _demCommands.put("indent", Defaults.INDENTATION);
        _demCommands.put("parindent", Defaults.PARAGRAPH_INDENTATION);
        _demCommands.put("textwidth", Defaults.TEXT_WIDTH);
        _demCommands.put("endnoteparskip", Defaults.ENDNOTE_PARAGRAPH_SKIP);
        _demCommands.put("endnoteindent", Defaults.ENDNOTE_INDENTATION);
        _demCommands.put("endnoteparindent",
                                        Defaults.ENDNOTE_PARAGRAPH_INDENTATION);
        _demCommands.put("endnotetextwidth", Defaults.ENDNOTE_TEXT_WIDTH);
        _demCommands.put("justify", 1);
        _demCommands.put("endnotejustify", 1);
        _demCommands.put("fill", 1);
        _demCommands.put("endnotefill", 1);
    }

    /** Start directing all formatted text to the endnote assembler. */
    void setEndnoteMode() {
        _endnoteMode = true;
    }

    /** Increment _refnum when comming across a endnote command. */
    void incrementRefNum() {
        _refNum += 1;
    }

    /** Return to directing all formatted text to _mainText. */
    void setNormalMode() {
        _endnoteMode = false;
    }

    /** True iff we are currently processing an endnote. */
    private boolean _endnoteMode;
    /** Number of next endnote. */
    private int _refNum;
    /** The text token (word) controller is currently holding. */
    private String _unfinished;
    /** L is the number of characters in _mainText. */
    private int l;
    /** lEnd is the number of characters in _endnoteText. */
    private int lEnd;
    /** The number of a line in a paragraph. */
    private int lineinparagraph;
    /** The number of a line in an endnote. */
    private int lineinendnote;
    /** PagePrinter printer to output text file. */
    private PagePrinter printer;
    /** LimeAssembler line that takes words from Controller and justifies. */
    private LineAssembler line;
    /** The indentation amount when justifying. */
    private int indentation;
    /** The current line in the page to split text in PagePrinter. */
    private int pageline;
}
