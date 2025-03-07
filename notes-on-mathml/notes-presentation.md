## Notes on Presentation MathML

  
### Automatic Linebreaking Algorithm (Informative)

One method of linebreaking that works reasonably well is sometimes
referred to as a "best-fit" algorithm. It works by computing a
"penalty" for each potential break point on a line. The break point
with the smallest penalty is chosen and the algorithm then works on
the next line. Three useful factors in a penalty calculation are:



 1. How much of the line width (after subtracting of the indent) is unused?
    The more unused, the higher the penalty.

 2. How deeply nested is the breakpoint in the expression tree? The
    expression tree's depth is roughly similar to the nesting depth of
    `mrow`s.  The more deeply nested the break point, the higher the
    penalty.

 3. Does a linebreak here make layout of the next line difficult?  If
    the next line is not the last line and if the indentingstyle uses
    information about the linebreak point to determine how much to
    indent, then the amount of room left for linebreaking on the next
    line must be considered; i.e., linebreaks that leave very little
    room to draw the next line result in a higher penalty.

 4. Whether `linebreak` has been specified: `nobreak` effectively sets
    the penalty to infinity, `badbreak` increases the penalty
    `goodbreak` decreases the penalty, and `newline` effectively sets
    the penalty to 0.
	
This algorithm takes time proportional to the number of token elements
times the number of lines.
  

  
### Linebreaking Algorithm for Inline Expressions (Informative)

   
A common method for breaking inline expressions that are too long for
the space remaining on the current line is to pick an appropriate
break point for the expression and place the expression up to that
point on the current line and place the remainder of the expression on
the following line.  This can be done by:
   

1.  Querying the text processing engine for the
    minimum and maximum amount of space available on the current line. 

2.   Using a variation of the automatic linebreaking algorithm given
     above), and/or using hints provided by [linebreak
     attributes](full#presm_lbattrs) on `mo` or `mspace`
     elements, to choose a line break.  The goal is that the first
     part of the formula fits "comfortably" on the current line while
     breaking at a point that results in keeping related parts of an
     expression on the same line.

3.   The remainder of the formula begins on the next line,
     positioned both vertically and horizontally according
     to the paragraph flow;
     MathML's [indentation attributes](full#presm_lbindent_attrs)
     are ignored in this algorithm.

4.   If the remainder does not fit on a line, steps 1 - 3 are repeated
     for the second and subsequent lines.  Unlike the for the first
     line, some part of the expression must be placed these lines so
     that the algorithm terminates.


### Warning about fine-tuning of presentation

Some use-cases require precise control of the math layout and
presentation.  Several MathML elements and attributes expressly
support such fine-tuning of the rendering.  However, MathML rendering
agents exhibit wide variability in their presentation of the the same
MathML expression due to difference in platforms, font availability,
and requirements particular to the agent itself (see 
[the Inroduction to Presentation MathML](full#presm_inro)).
The overuse of explicit rendering control
may yield a &#x2018;perfect&#x2019; layout on one platform, but give
much worse presentation on others.  The following sections clarify the
kinds of problems that can occur.

  
### Warning: non-portability of &#x201c;tweaking&#x201d;

For particular expressions, authors may be tempted to use the
[`mpadded`](full#presm_mpadded),
[`mspace`](full#presm_mspace),
[`mphantom`](full#presm_mphanom),
[`mtext`](full#presm_mtext)
elements to improve (&#x201c;tweak&#x201d;) the spacing generated by a specific renderer.

Without explicit spacing rules, various MathML renders may use
different spacing algorithms.  Consequently, different MathML
renderers may position symbols in different locations relative to each
other.  Say that renderer B, for example, provides improved spacing
for a particular expression over renderer A.  Authors are strongly
warned that &#x201c;tweaking&#x201d; the layout for
renderer A may produce very poor results in renderer B, very likely
worse than without any explicit adjustment at all.

Even when a specific choice of renderer can be assumed, its spacing
rules may be improved in successive versions, so that the effect of
tweaking in a given MathML document may grow worse with time. Also,
when style sheet mechanisms are extended to MathML, even one version
of a renderer may use different spacing rules for users with different
style sheets.

Therefore, it is suggested that MathML markup never use `mpadded` or `mspace`
elements to tweak the rendering of specific expressions, unless the
MathML is generated solely to be viewed using one specific version of
one MathML renderer, using one specific style sheet (if style sheets
are available in that renderer).

In cases where the temptation to improve spacing proves too strong,
careful use of `mpadded`, `mphantom`, or the [alignment
elements](full#presm_malign) may give more portable results than the
direct insertion of extra space using `mspace` or `mtext`. Advice given to the implementers of
MathML renderers might be still more productive, in the long run.
  

  
### Warning: spacing should not be used to convey meaning

MathML elements that permit &#x201c;negative
spacing&#x201d;, namely `mspace`,
`mpadded`, and `mo`, could in theory be used to simulate new
notations or &#x201c;overstruck&#x201d; characters by the
visual overlap of the renderings of more than one MathML
sub-expression.

This practice is _strongly discouraged in all situations_,
for the following reasons:
   
 


*    it will give different results in different MathML renderers
     (so the warning about &#x201c;tweaking&#x201d; applies), especially
     if attempts are made to render glyphs outside the bounding box of
     the MathML expression;
     
*    it is likely to appear much worse than a more standard construct
     supported by good renderers;



*    such expressions are almost certain to be uninterpretable
     by audio renderers, computer algebra systems,
     text searches for standard symbols,
     or other processors of MathML input.


   

More generally, any construct that uses spacing to convey mathematical
meaning, rather than simply as an aid to viewing expression structure,
is discouraged. That is, the constructs that are discouraged are those
that would be interpreted differently by a human viewer of rendered
MathML if all explicit spacing was removed.

Consider using the  [`mglyph`](full#presm_mglyph),element for cases such as this.  If
such spacing constructs are used in spite of this warning, they should
be enclosed in a `semantics` element that
also provides an additional MathML expression that can be interpreted
in a standard way. See [Annotating MathML](full#mixing_annotation_elements) for
further discussion.
   

The above warning also applies to most uses of rendering attributes to
alter the meaning conveyed by an expression, with the exception of
attributes on `mi` (such as `mathvariant`) used to distinguish one variable
from another.
  

### Recommendations on the use of invisible operators
The reasons for using specific `mo` elements for invisible operators include:
* such operators should often have specific effects on visual
     rendering (particularly spacing and linebreaking rules) that are not
     the same as either the lack of any operator, or spacing represented by
     `mspace` or `mtext`
     elements;
* these operators should often have specific audio renderings
     different than that of the lack of any operator;</p>
* automatic semantic interpretation of MathML presentation elements
     is made easier by the explicit specification of such operators.

For example, an audio renderer might render <i class="var">f</i>(<i class="var">x</i>)
   (represented as in the above examples) by speaking &#x201c;f of x&#x201d;, but use
   the word &#x201c;times&#x201d; in its rendering of <i class="var">x</i><i class="var">y</i>.
   Although its rendering must still be different depending on the structure
   of neighboring elements (sometimes leaving out &#x201c;of&#x201d; or
   &#x201c;times&#x201d; entirely), its task is made much easier by the use of
   a different `mo` element for each invisible
   operator.

### Names for other special operators
MathML also includes `DifferentialD` (U+2146) for use
in an `mo` element representing the differential
operator symbol usually denoted by &#x201c;d&#x201d;.  The reasons for
explicitly using this special character are similar to those for using
the special characters for invisible operators described in the
preceding section.

Note that there are other special characters that convey more meaning than their ASCII look-alike character such as `ExponentialE` (U+2147).