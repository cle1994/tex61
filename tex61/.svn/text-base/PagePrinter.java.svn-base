package tex61;

import java.io.PrintWriter;


/** A PageAssembler that sends lines immediately to a PrintWriter, with
 *  terminating newlines.
 *  @author Christian Le
 */
class PagePrinter {

    /** A new PagePrinter that sends lines to OUT. */
    PagePrinter(PrintWriter out) {
        _out = out;
    }

    /** Print LINE to my output. */
    void write(String line) {
        if (line.equals("")) {
            _out.println();
        } else {
            _out.println(line);
        }
    }

    /** PrintWriter that creates justified output file. */
    private final PrintWriter _out;
}
