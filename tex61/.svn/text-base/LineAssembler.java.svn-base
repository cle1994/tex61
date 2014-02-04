package tex61;

import java.util.ArrayList;
import java.math.BigDecimal;
import java.math.RoundingMode;

/** An object that receives a sequence of words of text and formats
 *  the words into filled and justified text lines that are sent to a receiver.
 *  @author Christian Le
 */
class LineAssembler {

    /** A new, empty line assembler with default settings of all
     *  parameters, sending finished lines to PAGES. */
    LineAssembler(PagePrinter pages) {
        _pages = pages;
        accumulatewords = "";
        accumulateendnotewords = "";
    }

    /** Formula for calculating the number of spaces to insert after
     *  word. WIDTH is the textwidth - indentation amount. NUMOFCHAR is
     *  is the total number of characters. NUMOFWORDS is the length of
     *  ArrayList. INDEX is the point(word) in the ArrayList. Returns
     *  the number of spaces between a word and the next word. */
    BigDecimal spacemaker(int width, int numOfChar,
                        int numOfWords, int index) {
        BigDecimal place = new BigDecimal(0.5);
        BigDecimal denom = new BigDecimal(numOfWords - 1);
        BigDecimal numer1 = new BigDecimal((width - numOfChar) * (index + 1));
        BigDecimal numer = new BigDecimal((width - numOfChar) * (index));
        BigDecimal rightside1 = numer1.divide(denom, 4, RoundingMode.CEILING);
        BigDecimal rightside = numer.divide(denom, 4, RoundingMode.CEILING);
        BigDecimal total1 =
                        rightside1.add(place).setScale(0, RoundingMode.DOWN);
        BigDecimal total =
                        rightside.add(place).setScale(0, RoundingMode.DOWN);
        BigDecimal space =
                    total1.subtract(total).setScale(4, RoundingMode.CEILING);
        return space;
    }

    /** Takes in VAL and converts it to spaces in strings. Returns spaces as
     *  a string. */
    String spacemakerToString(int val) {
        String space = "";
        int i = 0;
        while (i < val) {
            space += " ";
            i += 1;
        }
        return space;
    }

    /** Takes in VAL and converst it to indentation spaces as a string.
     *  Returns indentation as a string. */
    String indentationToString(int val) {
        String space = "";
        int i = 0;
        while (i < val) {
            space += " ";
            i += 1;
        }
        return space;
    }

    /** Creates lines when nofill is active. Takes in an array NOFILL,
     *  INDENTATION amount, REF number, ENDNOTELINE, and ENDNOTE mode.
     *  Returns a string with nofill active. */
    String nofilljustifier(ArrayList<String> nofill, int indentation, int ref,
                            int endnoteline, boolean endnote) {
        String indentSpace = indentationToString(indentation);
        String doneaccumulate = "";
        if (nofill.isEmpty()) {
            doneaccumulate = "";
        }
        accumulatewords += indentSpace;
        if (endnote && endnoteline == 0) {
            accumulatewords += "[" + Integer.toString(ref) + "] ";
        }
        for (int i = 0; i < nofill.size(); i += 1) {
            accumulatewords += nofill.get(i) + " ";
        }
        doneaccumulate = accumulatewords;
        newLine();
        return doneaccumulate;
    }

    /** Sets the ENDNOTE mode from Controller. */
    void getEndnoteMode(boolean endnote) {
        _endnote = endnote;
    }

    /** Sets the REF num from Controller. */
    void getRefNum(int ref) {
        _ref = ref;
    }

    /** Takes in NEEDSJUSTIFICATION, the words, WIDTH, textwidth - indentation,
     *  NUMOFCHAR, number of characters in the words, NUMOFWORDS, legth of list,
     *  INDENTATION, indentation amount, REFSPACES, spaces after reference num,
     *  ENDNOTE, check endnotemod, REF, reference number, ENDNOTELINE, line in
     *  endnote, JUSTIFY, check to justify or not, and IND, the indentation and
     *  justifies the text to fit the parameters. Returns justified line. */
    String justifier(ArrayList<String> needsjustification, int width,
                    int numOfChar, int numOfWords, int indentation,
                    int endnoteline, int justify,
                    String refspaces) {
        String indentSpace = indentationToString(indentation);
        String doneaccumulate = "";
        if (needsjustification.isEmpty()) {
            return doneaccumulate;
        }
        accumulatewords += indentSpace;
        if (_endnote && endnoteline == 0) {
            accumulatewords += "[" + Integer.toString(_ref) + "]"
                                    + refspaces;
        }
        for (int i = 0; i < numOfWords; i += 1) {
            String space = " ";
            if (justify == 0) {
                space = " ";
            } else if (justify == 1 && numOfWords != 1) {
                BigDecimal spaces;
                if (_endnote && endnoteline == 0) {
                    spaces = spacemaker(width - refspaces.length()
                                    - Integer.toString(_ref).length()
                                    - 2, numOfChar, numOfWords, i);
                } else {
                    spaces = spacemaker(width, numOfChar, numOfWords, i);
                }
                int numofspaces = spaces.intValue();
                if (numofspaces >= 3 * (numOfWords - 1)) {
                    numofspaces = 3;
                }
                space = spacemakerToString(numofspaces);
            } else if (numOfWords == 1) {
                space = "";
            }
            accumulatewords += needsjustification.get(i) + space;
        }
        doneaccumulate = accumulatewords;
        newLine();
        return doneaccumulate;
    }

    /** Takes in LINE, words, INDENTATION, total indentation, REFSPACES, spaces
     *  spaces after refnum, ENDNOTE, if endnotemod, REF, reference num,
     *  ENDNOTELINE, and IND, the indent and justifies a line that does not
     *  reach text width. Returns a line that does not go to next line. */
    String shortline(ArrayList<String> line, int indentation, boolean endnote,
                    int ref, int endnoteline, String refspaces) {
        String indentSpace = indentationToString(indentation);
        String doneaccumulate = "";
        if (line == null) {
            return "";
        }
        if (endnote) {
            accumulateendnotewords += indentSpace;
            if (endnoteline == 0) {
                accumulateendnotewords += "[" + Integer.toString(ref) + "]"
                                        + refspaces;
            }
            for (int i = 0; i < line.size(); i += 1) {
                accumulateendnotewords += line.get(i) + " ";
            }
            doneaccumulate = accumulateendnotewords;
        } else {
            accumulatewords += indentSpace;
            for (int i = 0; i < line.size(); i += 1) {
                accumulatewords += line.get(i) + " ";
            }
            doneaccumulate = accumulatewords;
            newLine();
        }
        accumulateendnotewords = "";
        return doneaccumulate;
    }

    /** Resets for a the next line of text. */
    void newLine() {
        accumulatewords = "";
    }

    /** Building endnotes. */
    private boolean _endnote;

    /** Reference number. */
    private int _ref;

    /** Destination given in constructor for formatted lines. */
    private final PagePrinter _pages;

    /** String of words that are being accumulated and justified to fit. */
    private String accumulatewords;
    /** String of words that are being accumlated and justified for
     *  referneces. */
    private String accumulateendnotewords;
}
