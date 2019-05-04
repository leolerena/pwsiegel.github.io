---
layout: post
permalink: /category-theory/galois-connections/
title: Galois Connections
abstract: A Galois connection is an adjunction between partially ordered sets (regarded as categories); they arise naturally all over the place in mathematics, and more recently they have found interesting applications to computer science.  This post covers the basic theory and explores some examples from graph theory and programming.
date: 2017-09-02
categories: category-theory 
tags: live
comments: true
---

Comparing is one of the most fundamental operations in mathematics and computer science: numbers can be compared by value, strings can be compared lexicographically, sets can be compared by containment, vertices in DAG's can be compared by ancestry, and so on.
A **partial ordering** is the minimal amount of structure required to make these sorts of comparisons.
There is often great insight to be gained by studying the interactions between different kinds of comparison: for instance, if one programming language has *more* restrictive syntax than another then one expects that it will be *less* expressive in an an appropriate sense.

The aim of this post is to introduce a theoretical tool called a **Galois connection** for organizing the interactions between different partial orderings.
After defining the notion of a partial ordering carefully, we will provide a partially ordered set with the structure of a category and then define the notion of a Galois connection using the concept of an adjunction between categories.
We will conclude with a few simple examples and some references to applications in the literature.

## Posets

Consider the set $\Z$ of all integers and the set $\Q$ of all rational numbers (i.e. ratios of integers).
These sets each come equipped with a relation $\leq$, where in both sets $a_1 \leq a_2$ simply means that $a_1$ is less than or equal to $a_2$.
This relation is satisfies a few simple axioms:

1. Reflexivity: $a_1 \leq a_1$ for all $a_1$
2. Antisymmetry: if $a_1 \leq a_2$ and $a_2 \leq a_1$ then $a_1 = a_2$
3. Transitivity: if $a_1 \leq a_2$ and $a_2 \leq a_3$ then $a_1 \leq a_3$

A relation $\leq$ on a set $A$ which satisfies these three properties is called a **partial ordering** on $A$, and $A$ equipped with $\leq$ is called a **poset** (short for "partially ordered set").

<div class="exercise">
Let $G$ be a simple graph.
Define a relation $\leq$ on the vertex set of $G$ by declaring that $v \leq w$ if either $v = w$ or there is a path of (directed) edges starting at $v$ and ending at $w$.
Show that $\leq$ is a partial ordering if and only if $G$ is acyclic.
</div>

Posets admit a natural category structure as we shall see below, and they provide a convenient source of examples for coming to terms with categorical structures like adjoints and monads.
In this post we will introduce the notion of a **Galois connection** (viewed as an adjunction between two posets) and briefly discuss an application to the theory of abstract interpretation in computer science.

## Posets as categories

The main point of this post is that any poset has the structure of a category, and that it can be useful and interesting to view it as such.

So let $(A, \leq)$ be a poset.
Consider the category (which, abusing notation, we will also refer to as $A$) whose objects are the elements of $A$ and which has exactly one morphism from $a_1$ to $a_2$ whenever $a_1 \leq a_2$.
We define composition as follows: if $f \colon a_1 \to a_2$ and $g \colon a_2 \to a_3$ are morphisms then by definition $a_1 \leq a_2$ and $a_2 \leq a_3$, so by transitivity $a_1 \leq a_3$ and thus we can define the composition $g \circ f$ to be the unique morphism from $a_1$ to $a_3$.

Let us check that these objects and morphisms really do determine a category; in other words, we must show that composition is associative and that each object has an identity morphism.

- **Associativity**

Given $f \colon a_1 \to a_2$, $g \colon a_2 \to a_3$, and $h \colon a_3 \to a_4$, the possible triple compositions are:

$$
h \circ (g \circ f) \quad \text{and} \quad (h \circ g) \circ f
$$

These compositions are equal since each is the unique morphism from $a_1$ to $a_4$.

- **Identity**

Given an object $a$ we have that $a \leq a$ by reflexivity of $\leq$, so define $1_a \colon a \to a$ to be the unique morphism from $a$ to itself.
Let us check that $1_a$ satisfies the axioms of an identity morphism.
Given a morphism $f \colon a \to a_0$, we have that

$$
f \circ 1_a
$$

is by definition of composition the unique morphism from $a$ to $a_0$.
But $f$ is a morphism from $a$ to $a_0$, so by uniqueness $f = f \circ 1_a$.
This shows that $1_a$ is a right identity; a similar argument shows that $1_a$ is also a left identity.

## Functors between posets

Let $(A, \leq_A$ and $(B, \leq_B)$ be two posets, viewed as categories according to the previous section.
Viewing these posets as categories, let us unravel the meaning of a functor $F \colon (A, \leq_A) \to (B, \leq_B)$.

A functor must map objects in $A$ to objects in $B$; since the objects in $A$ and $B$ correspond to elements of the underlying sets, this means that $F$ is a function from the set $A$ to the set $B$.
But $F$ also maps each morphism $f \colon a_1 \to a_2$ to a morphism $F(f) \colon F(a_1) \to F(a_2)$.
The condition that there is a morphism $a_1 \to a_2$ means $a_1 \leq_A a_2$, and the condition that there is a morphism $F(a_1) \to F(a_2)$ means $F(a_1) \leq_B F(a_2)$.

So a $F$ determines a function $A \to B$ which preserves the partial ordering, in the sense that $F(a_1) \leq_B F(a_2)$ whenever $a_1 \leq_A a_2$.
The reader is invited to check that any such function determines a functor $(A, \leq_A) \to (B, \leq_B)$, so a functor between posets is the same thing as a function between the underlying sets which preserves the partial ordering.

<div class="example">
Consider the integers $\Z$ and the rational numbers $\Q$ equipped with the standard partial ordering $\leq$.
The inclusion $I \colon \Z \to \Q$ which sends an integer $n$ to itself (viewed as the fraction $\frac{n}{1}$) preserves $\leq$ and hence defines a functor from the poset $\Z$ to the poset $\Q$.

Moreover the function $D \colon \Q \to \Z$ which rounds down each rational number also preserves $\leq$ and hence defines a functor from $\Q$ to $\Z$.
</div>

<div class="exercise">
Let $\phi \colon G_1 \to G_2$ be a homomorphism between directed acyclic graphs and let $\phi_V \colon V_1 \to V_2$ be the restriction of $\phi$ to the vertex sets.
Show that $\phi_V$ is a functor of posets where $V_1$ and $V_2$ are equipped with the partial ordering defined in the previous exercise.
</div>

## Galois connections - background

Before diving into the notion of a Galois connection, it might be worth explaining the terminology a bit.

Galois theory is a set of tools used in abstract algebra to understand the roots of a polynomial equation.
It works by using symmetry (the theory of groups) to understand the number systems (the theory of fields) which are obtained by adjoining the roots of such an equation.
For instance, if one adjoins the roots of the polynomial $x^2 - 2$ to the field $\Q$ of rational numbers then one obtains a larger field $\Q[\sqrt{2}]$ which differs from $\Q$ by a single nontrivial symmetry: the one which exchanges $\sqrt{2}$ with $-\sqrt{2}$.
In this sense, the cyclic group $C_2$ of order 2 (containing the identity and one nontrivial symmetry) captures the structure of the solutions to the equation $x^2 - 2 = 0$ with respect to $\Q$.

This can be taken further.
Suppose a field $L$ is obtained from $\Q$ by adjoining the roots of a polynomial.
Under some additional mild technical assumptions, $L$ is called a **Galois extension** of $\Q$ and $L$ has a Galois group $G$ which captures its symmetries with respect to $\Q$.
In this case the fundamental theorem of Galois theory says that there is a perfect correspondence between the intermediate subfields $\Q \subseteq K \subseteq L$ of $L$ and subgroups $G \supseteq H \supseteq 1$ of the Galois group, yielding a powerful tool for understanding the solutions of a complex polynomial equation.

Now, the set of all intermediate fields between $\Q$ and $L$ is partially ordered by the containment relation $\subseteq$, as is the set of all subgroups of a group $G$.
So the fundamental theorem of Galois theory can be expressed as a certain duality relationship between the poset of intermediate field extensions and the poset of subgroups of the Galois group.
One of the main slogans in category theory is that behind every duality lurks an adjunction, and in honor of Galois theory we arrive at the following definition:

<div class="definition">
Let $(A, \leq_A)$ and $(B, \leq_B)$ be two posets.
A **Galois connection** between $A$ and $B$ is an [adjunction][1] between $A$ and $B$ viewed as categories via their poset structures.
</div>

In the literature it is common to make a distinction between **monotone** and **antitone** Galois connections because it is typical for a Galois connection to transform increasing sequences in $A$ into decreasing sequences in $B$.
(This is the case in the original application to Galois theory, for instance.)
But from the point of view expressed here a decreasing sequence in $B$ is just an increasing sequence in $B$ with the partial ordering reversed, or equivalently in the opposite category $B^{op}$.
So we have suppressed the distinction in the formal definition with the understanding that some care must be taken to choose the correct partial ordering in applications.

## Galois connections made easier

Let $F \colon B \to A$ and $G \colon A \to B$ be functions between posets $(A, \leq_A)$ and $(B, \leq_B)$ which preserve the partial orderings.
We saw in a previous section that $F$ and $G$ can be viewed as functors between $A$ and $B$ equipped with their natural category structure.
The question we aim to answer here is: when do $F$ and $G$ form an adjunction (and therefore a Galois connection)?

The definition of adjunction provides a quick answer: there must exist a certain pair of natural transformations with certain properties.
But the definition of adjunction can be a bit obtuse, so the goal of the next result is to translate it into the more down-to-earth language of partial orderings.

<div class="proposition">
Let $F$ and $G$ be partial-order-preserving functions between posets $A$ and $B$ as above.  Then $F$ and $G$ form a Galois connection if and only if

$$
\begin{equation} \label{gal_connection}
   F(b) \leq_A a \iff b \leq_B G(a)
\end{equation}
$$

for all $a \in A$, $b \in B$.
</div>
<div class="proof">
To begin, assume that $F$ and $G$ form a Galois connection.
This means there is a unit / counit pair of natural transformations:

$$
u \colon 1_B \to GF \quad \text{and} \quad v \colon FG \to 1_A
$$

satisfying the adjunction identities $vF \circ Fu = 1_F$ and $Gv \circ uG = 1_G$.
The component of $v$ at an object of $A$ (i.e. a point $a \in A$) is a morphism $v_a \colon FG(a) \to a$.
The existence of this morphism is equivalent to the statement that $FG(a) \leq_A a$, so the existence of $v$ is equivalent to the statement that this inequality holds for all $a \in A$.
Similarly, the existence of $u$ is equivalent to the statement that $b \leq_B GF(b)$ for all $b \in B$.

Now, take $a \in A$ and $b \in B$ such that $F(b) \leq_A a$.
Since $G$ preserves $\leq_A$ we have $GF(b) \leq_B G(a)$, and since $b \leq_B GF(b)$ we get that $b \leq_B G(a)$ by transitivity.
A similar argument shows that $b \leq_B G(a)$ implies $F(b) \leq_A a$ as well, so we have shown that every Galois connection satisfies the condition \eqref{gal_connection}.
(We did not need to use the adjunction identities - only the existence of the counit/unit pair.)

Conversely, suppose $F$ and $G$ satisfy the condition \eqref{gal_connection}; we aim to prove that they form a Galois connection.

For any $a \in A$ we have $G(a) \leq_B G(a)$ by reflexivity, so by \eqref{gal_connection} we have $FG(a) \leq_A a$.
By the definition of the category structure on $A$ this means there is a unique morphism from $FG(a)$ to $a$.
Call this morphism $v_b$; let us show that the $v_b$'s together form a natural transformation $v \colon FG \to 1_A$.

To do this we must check that for any morphism $f \colon a_1 \to a_2$ we have:

$$v_{a_2} \circ FG(f) = 1_A(f) \circ v_{a_1}$$

$FG(f)$ is the unique morphism from $FG(a_1)$ to $FG(a_2)$, so $v\_{a_2} \circ FG(f)$ is the unique morphism from $FG(a_1)$ to $a_2$.
On the right-hand side, $1_a(f)$ is just $f$ and $v\_{a_1}$ is the unique morphism from $FG(a_1)$ to $FG(a_2)$, so the composition is again the unique morphism from $FG(a_1)$ to $a_2$.
This proves that $v$ is indeed a natural transformation; it will be the counit of the adjunction between $F$ and $G$.

The construction of the unit $u$ is similar: for any $b \in B$ we have $F(b) \leq_A F(b)$ by reflexivity, and so $b \leq_B GF(b)$ by \eqref{gal_connection}.
So we can define $u_b$ to be the unique moprhism from $b$ to $GF(b)$, and we leave it to the reader to verify that these morphisms assemble to form a natural transformation $u \colon 1_B \to GF$.

It remains only to show that $F$, $G$, $u$, and $v$ satisfy the adjunction identies; we shall prove the identity $vF \circ Fu = 1_F$ here and leave the identity $Gv \circ uG = 1_G$ to the reader.

Working component-wise, recall that $(vF \circ Fu)_b = v\_{F(b)} \circ F(u_b)$.
$u_b$ is by definition the unique morphism from $b$ to $GF(b)$, so $F(u_b)$ is the unique morphism from $F(b)$ to $FGF(b)$.
On the other hand $v\_{F(b)}$ is by definition the unique morphism from $FGF(b)$ to $F(b)$, so the composition $v\_{F(b)} \circ F(u_b)$ is the unique morphism from $F(b)$ to itself, as desired.
</div>

<div class="example">
Let $I \colon \Z \to \Q$ and $D \colon \Q \to \Z$ be the inclusion and rounding down maps, respectively.
If $I(n) \leq r$ for $n \in \Z$ and $r \in \Q$ then $DI(n) \leq D(r)$ which in turn implies $D(r)$.
Conversely, if $n \leq D(r)$ then $I(n) \leq ID(r) \leq r$.
By the previous propsotion $(I, D)$ is a Galois connection.
</div>

The reader is invited to check that $(U, I)$ is a Galois connection where $I \colon \Z \to \Q$ is as above and $U \colon \Q \to \Z$ is the rounding up map.
This explains the slogan "left adjoints round up, right adjoints round down" which reflects the general principle that left adjoints tend to *add* structure while right adjoints tend to *forget* it.

## Applications

Galois connections show up all over the place in mathematics, and they appear quite a bit in computer science as well.
In a future post I will use them as a source of examples of monads; for now I will provide a simple exercise involving graph theory and some references to the literature for further applications.

<div class="exercise">
Let $G$ be a directed acyclic graph and let $\leq$ denote the partial ordering on its vertex set $V$ defined above.
Let $H$ be a subgraph of $G$, and let $I \colon H \to G$ be the inclusion homomorphism.
Construct a right adjoint for the restriction of $I$ to the vertex set of $H$.
</div>

A couple applications oriented toward computer science:

- [Widening Operators for Abstract Interpretation][2] (Cortesi): Abstract interpretation is a technique for understanding the semantics of a computer program without having to execute it.  Many modern approaches are very heavily organized around Galois connections.

- [The Galois Connection between Syntax and Symantics][3] (Smith): There is a slogan in logic due to Lawvere: "Syntax and Semantics are adjoint".  Roughly, there is a functor which sends an axiomatic system to the set of all structures which satisfy it, and another functor which sends a set of structures to its minimal axiomatization.  The set of all axiomatic systems and the power set of all mathematical structures are both posets, and these functors form a Galois connection.


[1]: {{ site.baseurl }}{% post_url math/category-theory/2017-09-02-adjunctions %} "Adjunctions"
[2]: http://www.dsi.unive.it/~cortesi/paperi/sefm08.pdf "Widening Operators for Abstract Interpretation"
[3]: http://www.logicmatters.net/resources/pdfs/Galois.pdf "The Galois Connection between Syntax and Symantics"
