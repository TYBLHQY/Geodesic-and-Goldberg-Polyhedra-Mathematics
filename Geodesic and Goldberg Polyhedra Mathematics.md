## Overview

In this design we restrict both polyhedra to ones built from an icosahedron base. A Geodesic polyhedron is built from a number of equilateral triangles. A Goldberg polyhedron is the dual of a Geodesic one and vice versa. A dual of a polyhedron swaps faces for vertices and vertices for faces.

![Dual](https://doc.babylonjs.com/img/snippets/geo1.png)

<center><strong>Fig 1 Icosahedron and its Dual</strong></center>

The simplest class of Geodesic polyhedra splits each face of an icosahedron into equilateral triangles.

![Geodesic](https://doc.babylonjs.com/img/snippets/geo2.png)

<center><strong>Fig 2 Geodesic Polyhedron</strong></center>

![Geodesic Sphere](https://doc.babylonjs.com/img/snippets/geo3.png)

<center><strong>Fig 3 Geodesic Polyhedron mapped to Sphere</strong></center>

More complex classes are formed by rotating, with restrictions, the isometric grid formed by the equilateral triangles that split each face.

## Geodesic Classes

A regular icosahedron is formed from 20 primary equilateral triangles. Rather than rotating the underlying grid we demonstrate the classes by rotating a primary triangle on a fixed isometric grid.

Rotations are formed by applying two positive integers $m$ and $n$. From a fixed point $O (0, 0)$ on the isometric grid form a point $A(m, n)$ which is m horizontal units from $O$ and n units along the positive gradient line. The primary triangle is formed with a point $B$ such that $OAB$ is an equilateral triangle. The Geodesic polyhedron so formed will be noted as $GD(m, n)$ and its dual the Goldberg polyhedron as $GP(m, n)$;

![primary 1](https://doc.babylonjs.com/img/snippets/geo5.png)

<center><strong>Fig 4 Creating a GD(5, 2) on Isometric Grid</strong></center>

![primary 2](https://doc.babylonjs.com/img/snippets/geo6.png)

<center><strong>Fig 5 Primary Triangle Grid Rotation 5, 2</strong></center>

There are three classes of these types of polyhedra

- Class I $GD(m, 0)$
- Class II $GD(m, m)$
- Class III $GD(m, n), m ≠ n$

For Class III $GD(m, n)$ and $GD(n, m)$ are reflections of each other as are $GP(m, n)$ and $GP(n, m)$. We only consider Class III $GD(m, n)$ where $m > n$.

## Geodesic Nets

![net 1](https://doc.babylonjs.com/img/snippets/geo4.png)

<center><strong>Fig 6 Net of Icosahedron for GD(5, 2)</strong></center>

From the net we can see that while some of the triangular faces that will make up the Geodesic mesh lie in the plane of a primary triangle others will lie across adjacent primary triangles

![net 2](https://doc.babylonjs.com/img/snippets/geo7.png)

<center><strong>Fig 7 Vertices of some triangular faces lie across adjacent primary triangles.</strong></center>

When the icosahedron is formed from the net these faces will not lie on any the plane of any primary triangle.

## Building the Geodesic Polyhedron

There are two main steps to this:

1. Grouping the vertices into three to form each face of the Geodesic polyhedron
2. Positioning the vertices in 3D space

In order to achieve both of these we need to understand transformations on an isomorphic grid and how to match three facet vertices across the edges of a primary triangle. Using Fig 7 as a basis, we can then label each $GD(m, n)$ vertex in a primary triangle using an isomorphic coordinate system and then use rotations of $60^o$ of a primary triangle to match up vertices to form the triangular facets of an adjacent primary triangle.

### Transformations on an Isomorphic Grid

In terms of the net all the $GD(m, n)$ vertices lie on an isometric grid and we will need to rotate them $60^o$ about given points. To understand how this is achieved we consider isometric vectors and their transformations.

The $x$ axis is horizontal and the angle between the $x$ axis and the $y$ axis is $60^o$

![axis](https://doc.babylonjs.com/img/snippets/geo11.png)

<center><strong>Fig 8 Isometric Axis</strong></center>

The unit vector along the $x$ axis is $\vec{i}$ and along the $y$ axis is $\vec{j}$, the unit vector $\vec{k} = -\vec{i} + \vec{j}$ which lies along the third line of an isometric grid is also useful.

![unit vectors](https://doc.babylonjs.com/img/snippets/geo12.png)

<center><strong>Fig 9 Unit Vectors</strong></center>

Consider the position vector $x\vec{i} + y\vec{j}$ its length $|x\vec{i} + y\vec{j}| = \sqrt{x^2 + y^2 + xy}$. This is the result of applying the cosine rule to the triangle in Fig 10

$$
\begin{aligned}
|x\vec{i} + y\vec{j}| &= \sqrt{x^2 + y^2 + 2xy * cos(120^o)} \\
&= \sqrt{x^2 + y^2 + 2xy * \frac{1}{2}} \\
&= \sqrt{x^2 + xy + y^2}
\end{aligned}
$$

![length](https://doc.babylonjs.com/img/snippets/geo13.png)

<center><strong>Fig 10 Unit Vectors</strong></center>

Vector additions still hold $x\vec{i} + y\vec{j} + a\vec{i} + b\vec{j} = (x + a)\vec{i} + (y + b)\vec{j}$

Luckily all our application needs only integer values for the vectors and rotations only multiples of $60^o$

Consider the rotation of the position vector $x\vec{i} + y\vec{j}$ $60^o$ about the origin $O$, where x and y are integers. The rotation about the origin can be $R^+$ (counter-clockwise purple to red) or $R^-$ (clockwise purple to blue) as in Fig 11

![rotation](https://doc.babylonjs.com/img/snippets/geo14.png)

<center><strong>Fig 11 Rotation about the origin</strong></center>

$$
\begin{aligned}
R^+(x\vec{i} + y\vec{j}) &= y\vec{k} + x\vec{j} \\
&= y(-\vec{i} + \vec{j}) + x\vec{j} \\
&= -y\vec{i} + (x + y)\vec{j}
\end{aligned}
$$

$$
\begin{aligned}
R^-(x\vec{i} + y\vec{j}) &= y\vec{i} - x\vec{k} \\
&= y\vec{i} - x(-\vec{i} + \vec{j}) \\
&= (x + y) - x\vec{j}
\end{aligned}
$$

Where $S^+$ and $S^-$ are respectively counter clockwise and clockwise rotations of $x\vec{i} + y\vec{j}$ $60^o$ about a point $a\vec{i} + b\vec{j}$

$$
\begin{aligned}
S^ + (x\vec{i} + y\vec{j}, a\vec{i} + b\vec{j}) &= R^+((x\vec{i} + y\vec{j}) - (a\vec{i} + b\vec{j})) + a\vec{i} + b\vec{j} \\
&= R^+((x - a)\vec{i}) + (y - b)\vec{j}) + a\vec{i} + b\vec{j} \\
&= -(y - b)\vec{i} + ((x - a) + (y - b))\vec{j} + a\vec{i} + b\vec{j} \\
&= (a + b - y)\vec{i} + (x + y - a)\vec{j}
\end{aligned}
$$

$$
\begin{aligned}
S^{-}(x\vec{i} + y\vec{j}, a\vec{i} + b\vec{j}) &= R^-((x\vec{i} + y\vec{j}) - (a\vec{i} + b\vec{j})) + a\vec{i} + b\vec{j} \\
&= R^-((x - a)\vec{i} + (y - b)\vec{j}) + a\vec{i} + b\vec{j} \\
&= (x - a + y - b)\vec{i} - (x - a)\vec{j} + a\vec{i} + b\vec{j} \\
&= (x + y - b)\vec{i} + (a + b - x)\vec{j}
\end{aligned}
$$

![rotation center](https://doc.babylonjs.com/img/snippets/geo22.png)

<center><strong>Fig 12 Symmetry Rotations 0°, 120°, -120°</strong></center>

We also need to consider rotations $120^o$ and $-120^o$ about the center of the triangle $OAB$.

Let $P$ have position vector $x\vec{i} + y\vec{j}$ which after a rotation of $120^o$ maps to $P_1$ and after a rotation of $-120^o$ maps to $P_2$

$$
\begin{aligned}
P_1 &= m\vec{i} + n\vec{j} - y\vec{i} + x\vec{k} \\
&= m\vec{i} + n\vec{j} - y\vec{i} - x\vec{i} + x\vec{j} \\
&= (m - x - y)\vec{i} + (n + x)\vec{j}
\end{aligned}
$$

$$
\begin{aligned}
P_2 &= -n\vec{i} + (m + n)\vec{j} - y\vec{k} - x\vec{j} \\
&= -n\vec{i} + (m + n)\vec{j} + y\vec{i} - y\vec{j} - x\vec{j} \\
&= (y - n)\vec{i} + (m + n - x - y)\vec{j}
\end{aligned}
$$

Where $RC^{+}$ is a rotation of $120^o$ and $RC^{-}$ is a rotation of $-120^o$ about the center of $OAB$

$$
\begin{align}
RC^+(x\vec{i} + y\vec{j}) &= (m - x - y)\vec{i} + (n + x)\vec{j} \\
RC^-(x\vec{i} + y\vec{j}) &= (y - n)\vec{i} + (m + n - x - y)\vec{j}
\end{align}
$$

### Match Vertices Across Edges of a Primary Face

We can label the face and vertices for each primary triangle as shown in the net for Fig 13. Taking $OA$ as the base, $OB$ as the left edge and $AB$ as the right edge of each face; every face has a face on its left, $F_L$, right, $F_R$, and base $F_B$

![net 2](https://doc.babylonjs.com/img/snippets/geo9.png)

<center><strong>Fig 13 Labelled Net</strong></center>

Depending on the position of the face the vertex alignment differs as can be seen in Fig 14.

![map 1](https://doc.babylonjs.com/img/snippets/geo10.png)

<center><strong>Fig 14 Face and Edge Relationships</strong></center>

Since $F$ shares edges with $F_R$, $F_L$ and $F_B$ any matching of $GD(m, n)$ vertices across the edges of F can be dealt with by considering the counter-clockwise rotations of various permutations of $F$, $F_R$, $F_L$ or $F_B$ around the vertices, $O$, $A$ or $B$, as shown in red in Fig 14. The 30 possible edges are all covered by the table below.

| From Face | To Face | About |     | From Face | To Face | About |
| --------- | ------- | ----- | --- | --------- | ------- | ----- |
| 0         | 1       | B     |     | 10        | 0       | O     |
| 1         | 2       | B     |     | 11        | 1       | O     |
| 2         | 3       | B     |     | 12        | 2       | O     |
| 3         | 4       | B     |     | 13        | 3       | O     |
| 4         | 0       | B     |     | 14        | 4       | O     |
| 5         | 10      | O     |     | 15        | 19      | B     |
| 5         | 14      | A     |     | 15        | 5       | A     |
| 6         | 11      | O     |     | 16        | 15      | B     |
| 6         | 10      | A     |     | 16        | 6       | A     |
| 7         | 12      | O     |     | 17        | 16      | B     |
| 7         | 11      | A     |     | 17        | 7       | A     |
| 8         | 13      | O     |     | 18        | 17      | B     |
| 8         | 12      | A     |     | 18        | 8       | A     |
| 9         | 14      | O     |     | 19        | 18      | B     |
| 9         | 13      | A     |     | 19        | 9       | A     |
<center><strong>Table 1</strong></center>

We can see that a rotation about $B$ aligns edge $AB$ and $OB$, about $O$ aligns edge $OB$ and $OA$ and about $A$ aligns $OA$ and $BA$.

We now consider obtaining the three vertices for the edge overlapping facets in more detail. We start with a rotation about $B$

![primary triangle](https://doc.babylonjs.com/img/snippets/geo15.png)

<center><strong>Fig 15 Rotation of Primary Triangle about the B for Left GD(5, 2), Right GD(6, 3)</strong></center>

Consider a primary triangle $OAB$ as in Fig 15. For triangle $OAB$ take $O$ as the origin of the isometric axes then each vertex (red sphere) can be given $(x, y)$ coordinates. For each $y$ there is a minimum (blue marker) and maximum (green) value of $x$ that belong to $OAB$. Rotating $OAB$ $60^o$ counter-clockwise about $B$ the minimum $x$ map to the orange markers in the rotation. As you can see in Fig 15 example there is overlap of maximum and minimum markers at the primary triangle vertices. For $GD(m, n)$ when $n$ is a factor of m then the maximum $x$ (green) of $OAB$ coincide with the rotated minimum $x$ (orange) for some facet vertices as well as at the primary triangle vertices.

We can also see that rows contain either one or two rotated minimums.

![overlaps](https://doc.babylonjs.com/img/snippets/geo16.png)

<center><strong>Fig 16 Facet Overlap Vertices</strong></center>

In Fig 16 we can see that there are three types of rotated minimum vertices 0, 1, 2 that produce facets that overlap the edge. Each of these points can be associated with two facet triangle, one that is up (light grey) and one down (dark grey).

For all facet vertices $x\vec{i} + y\vec{j}$ of a primary triangle $OAB$ of $GD(m, n)$ let $M^{max}(y)$ be the point with maximum $x$ value and $M^{min}(y)$ the point with minimum $x$ value for row $y$.

Let $P = x_p\vec{i} + y_p\vec{j}$ be the position vector of a rotated minimum $x\vec{i} + y\vec{j} = M^{min}(y)$.

When $P = M^{max}(y_p)$ the up and down facets lie inside the primary triangle are thus ignored in creating overlapping facets.

For an up facet When $P$ is of type 0 and 1 the facet (light grey) has vertices $P$, $M^{max}(y_p - 1)$, $P + \vec{i} - \vec{j}$

When $P$ is of type 2 the facet (light grey) has vertices $P$, $M^{max}(y_p - 1) - \vec{i}$, $M^{max}(y_p - 1)$

For a down facet When $P$ is of type 0 the facet (dark grey) has vertices $P$, $M^{max}(y_p)$, $M^{max}(y_p - 1)$

When $P$ is of type 1 the facet (dark grey) has vertices $P$, $P - \vec{i}$, $M^{max}(y_p - 1)$

When $P$ is of type 2 the facet (dark grey) has vertices $P$, $M^{max}(y_p)$, $M^{max}(y_p - 1) - \vec{i}$

This range of vertex triples forms all the overlapping facets.

Considering Fig 15 these position vectors are all relative to the origin $O$ of primary triangle $F$. However for facet vertices in $F_R$ we need the position vectors of these vertices to be relative to the origin O of $F_R$. Rotating $F_R 60^o$ clockwise will return $F_R$ to the primary triangles position as in Fig 4

For each vertex in the triples based on $P$ we just need to apply $S^{-}$. Since $S^{-}(S^{+})$ is the identity $S^{-}(P, B) = x\vec{i} + y\vec{j}$

For $x\vec{i} + y\vec{j}$
Type 1 occurs when $P_\vec{j} = S^{+}(M^{min}(y_p + 1), B_\vec{j})$
Type 2 occurs when $P_{\vec{j}} ≠ M^{max}(y_p - 1)$
Otherwise $P$ is of type 0.

$$
\begin{aligned}
P &= S^{+}(x\vec{i} + y\vec{j}, B) \\
&= S^{+}(x\vec{i} + y\vec{j}, -n\vec{i} + (m + n)\vec{j}) \\
&= (-n + m + n - y)\vec{i} + (x + y + n)\vec{j} \\
&= (m - y)\vec{i} + (x + y + n)\vec{j}
\end{aligned}
$$

$$
\begin{aligned}
P + \vec{i} - \vec{j} &= (m - y)\vec{i} + (x + y + n)\vec{j} + \vec{i} - \vec{j} \\
&= (m - (y - 1))\vec{i} + (x + (y - 1) + n)\vec{j} \\
&= S^{+}(x\vec{i} + (y - 1)\vec{j}, B);
\end{aligned}
$$

$$
\begin{aligned}
P - \vec{i} &= (m - y)\vec{i} + (x + y + n)\vec{j} - \vec{i} \\
&= (m - (y + 1))\vec{i} + (x - 1 + (y + 1) + n)\vec{j} \\
&= S^{+}((x - 1)\vec{i} + (y + 1)\vec{j}, B);
\end{aligned}
$$

If follows that the overlapping facet vertex triples are given by

| P Type | Triangle      | Vertex Triple                                                                            |
| ------ | ------------- | ---------------------------------------------------------------------------------------- |
| 0, 1   | Triangle Up   | Vertex Triple $M_{F_R}^{min}(y)$, $M_{F}^{max}(y_p - 1)$, $M_{F_R}^{min}(y - 1)$         |
| 2      | Triangle Up   | Vertex Triple $M_{F_R}^{min}(y)$, $M_{F}^{max}(y_p - 1) - \vec{i}$, $M_F^{max}(y_p - 1)$ |
| 0      | Triangle Down | Vertex Triple $M_{F_R}^{min}(y)$, $M_{F}^{max}(y_p)$, $M_F^{max}(y_p - 1)$               |
| 1      | Triangle Down | Vertex Triple $M_{F_R}^{min}(y)$, $M_{F_R}^{min}(y + 1)$, $M^{max}(y_p - 1)$             |
| 2      | Triangle Down | Vertex Triple $M_{F_R}^{min}(y)$, $M_{F}^{max}(y_p)$, $M_F^{max}(y_p - 1) - \vec{i}$     |
<center><strong>Table 2</strong></center>

A rotation about $O$ is as in Fig 17

![rotation O](https://doc.babylonjs.com/img/snippets/geo17.png)

<center><strong>Fig 17 Rotation OAB 60° about O</strong></center>

This can also be achieved by rotating the diagrams in Fig 15 $120^o$ counter-clockwise as in Fig 18

![rotation A](https://doc.babylonjs.com/img/snippets/geo18.png)

<center><strong>Fig 18 Rotation Fig 15 120° about O</strong></center>

Hence applying a rotation of $120^o$ to the values in Table 2 will produce the correct vertex isometric positions for the overlapping facets of edges $OA$ and $OB$

$A$ rotation about $A$ is as in Fig 19

![rotation O](https://doc.babylonjs.com/img/snippets/geo19.png)

<center><strong>Fig 19 Rotation OAB 60° about A and translation</strong></center>

This can also be achieved by rotating the diagrams in Fig 15 $-120^o$ counter-clockwise and translating $-m\vec{i} - n\vec{j}$ as in Fig 20

![rotation A](https://doc.babylonjs.com/img/snippets/geo20.png)

<center><strong>Fig 20 Rotation Fig 15 -120° about A and translation</strong></center>

Hence applying a rotation of $-120^o$ about A to the values in Table 2 followed by a translation of $-m\vec{i} - n\vec{j}$ will produce the correct vertex isometric positions for the overlapping facets of edges $OA$ and $AB$.

### Facet Isometric Coordinates

For each facet vertex that lies on within the primary triangle we determine the isometric coordinates (x, y). When constructing the primary triangle for GD(m, n) $O$ is (0, 0), A is (m, n) and $B$ is (-n, m + n) these are the white vertices in Fig 21

![facet vertices](https://doc.babylonjs.com/img/snippets/geo21.png)

<center><strong>Fig 21 Internal Facet Vertices</strong></center>

The red vertices lie on the maximum equilateral triangle $CDE$, that can be drawn wholly inside $OAB$ along grid lines. The bottom left red vertex lies on the intersection of the $y$ axis and the line through $A$ parallel to the $x$ axis and so has iso-coordinates (0, n).

![Rotational Symmetry](https://doc.babylonjs.com/img/snippets/geo22.png)

<center><strong>Fig 22 Rotating (x, y) 0°, 120°, -120°</strong></center>

Therefore by rotational symmetry

$$
\begin{aligned}
D &= RC+(0, n) = (m - n, n) \\
E &= RC-(0, n) = (0, m)
\end{aligned}
$$

Now consider the green vertices, they all lie on triangles similar to $OCA$ as in Fig 23 and note that $OA = n$ and $CA = m$

![Green Vertices](https://doc.babylonjs.com/img/snippets/geo23.png)

<center><strong>Fig 23 Green Vertices</strong></center>

Let $F$ be the point (0, y) with $y < n$ and $G$ the point (x, y). Let $H$ be the point where the extended line through $FG$ meets $OA$. It follows that $x ≤ FH$. Note that

![Similar Triangles](https://doc.babylonjs.com/img/snippets/geo24.png)

<center><strong>Fig 24 Similar Triangles</strong></center>

Triangles $OCA$ and $OFH$ are similar and so

$$
\begin{align}
FH &= OF \\
CA &= 2OC
\end{align}
$$

and so

$$
FH = y_{(m / n)}, since (x ≤ FH, x ≤ y_{(m / n)})
$$

The green vertices all lie on the iso-grid and so are all points (x, y) with integer values for x and y where

$0 < y ≤ n$ and $0 ≤ x ≤ y_{(m / n)}$ Using rotation symmetry and rotating about the center of $OAB$ $120^o$ and $-120^o$ gives the yellow and blue vertices.

![Along edge](https://doc.babylonjs.com/img/snippets/geo29.png)

<center><strong>Fig 25 Facet vertices along edges when m and n ≠ 0 have factors in common and n ≠ 0</strong></center>

Any facet vertices lying on an edge of a primary triangle will have the same position as that on a matched edge.

When $n = 0$, there is no rotation and there are $m - 1$ shared positions along each edge between the vertices.

When $n ≠ 0$, let $g$ be the $HCF$ (highest common factor) of $m$ and $n$. When $n = 1$ there are no shared positions along each edge between vertices. When $n > 1$ let $m_1$ and $n_1$ be such that $m = gm_1$ and $n = gn_1$ then the number of shared positions between the vertices along one edge is $g - 1$.

Along $OA$ they occur at $m_1\vec{i} + n_1\vec{j}, 2m_1\vec{i} + 2n1\vec{j}, ......, (g - 1)m_1\vec{i} + (g - 1)n_1\vec{j}$

Along $AB$ at $R^{C^+}(m_1\vec{i} + n_1\vec{j}), R^{C^+}(2m_1\vec{i} + 2n_1\vec{j}), ......, R^{C^+}((g - 1)m_1\vec{i} + (g - 1)n_1\vec{j}$

Along $BO$ at $R^{C^-}(m_1\vec{i} + n_1\vec{j}), R^{C^-}(2m_1\vec{i} + 2n_1\vec{j}), ......, R^{C^-}((g - 1)m_1\vec{i} + (g - 1)n_1\vec{j}$

### Cartesian Coordinates

![Carteian](https://doc.babylonjs.com/img/snippets/geo27.png)

<center><strong>Fig 26 Cartesian Coordinates</strong></center>

Fig 26 shows that the cartesian coordinates of a point with position iso-vector $x\vec{i} + y\vec{j}$
has cartesian coordinates $(x + \frac{1}{2}y,\frac{\sqrt{3}}{2}y)$

### Mapping Primary Triangle to Icosahedron Face

![Primary Map](https://doc.babylonjs.com/img/snippets/geo28.png)

<center><strong>Fig 27 Primary Triangle Mapping to Icosahedron Face</strong></center>

Let $OAB$ be an equilateral triangle with a Cartesian frame of reference with units vectors $\vec{u}$ and $\vec{v}$ along the $x$ and $y$ axes respectively and $\vec{i}$ and $\vec{j}$ the usual iso-vectors. $O_1A_1B_1$ and the frame of reference $X_1Y_1$ are formed by a rotation and scaling of $OAB$ and $XY$ in 3D space with $\vec{u_1}$ and $\vec{v_1}$ and $\vec{i_1}$ and $\vec{j_1}$ the corresponding Cartesian and iso Vectors.

Let $P$ be any point with cartesian position vector $x\vec{u} + y\vec{v}$. Let $d$ be the scaling of $O_1A_1B_1$ to $OAB$. The corresponding point $P_1$ to $P$ with have position vector $x d\vec{u_1} + y d\vec{v_1}$.

In iso_vectors $OA = m\vec{i} + n\vec{j}$ and $OB = -n\vec{i} + (m + n)\vec{j}$ and so in Cartesian vectors

$$
\begin{align}
OA &= (m + \frac{1}{2}n)\vec{u} +\frac{\sqrt{3}}{2}n\vec{v} \\
OB &= (\frac{1}{2}(m + n) - n)\vec{u} +\frac{\sqrt{3}}{2}(m + n)\vec{v} = \frac{1}{2}(m - n)\vec{u} +\frac{\sqrt{3}}{2}(m + n)\vec{v} \\

(m + \frac{1}{2}n)OB &= (m + \frac{1}{2}n)(\frac{1}{2}(m - n))\vec{u} +\frac{\sqrt{3}}{2}(m + n)(m + \frac{1}{2}n)\vec{uv} \\
(\frac{1}{2}(m - n))OA &= (m + \frac{1}{2})(\frac{1}{2}(m - n))\vec{u} +\frac{\sqrt{3}}{2}n(\frac{1}{2}(m - n))\vec{v} \\

(m + \frac{1}{2}n)OB - (\frac{1}{2}(m - n))OA &=\frac{\sqrt{3}}{2}((m + n)(m + \frac{1}{2}n) - n(\frac{1}{2}(m - n)))\vec{v} \\
(2m + n)OB - (m - n)OA &= \sqrt{3}(m^2 + \frac{1}{2}n^2 + mn + \frac{1}{2}mn - \frac{1}{2}mn + \frac{1}{2}n^2)\vec{v} \\
(2m + n)OB - (m - n)OA &= \sqrt{3}(m^2 + n^2 + mn)\vec{v}
\end{align}
$$

from which we can find $\vec{v}$ and then $\vec{u}$ in terms of $OA$ and $OB$.

Since $O_1A_1 = m d\vec{i_1} + n d\vec{j_1}$ and $O_1B_1 = -n d\vec{i_1} + (m + n)d\vec{j_1}$

we can use this method to find $\vec{v_1}$ and then $\vec{u_1}$ in terms of $O_1A_1$ and $O_1B_1$ so that we can determine $P_1$.

For example when finding $\vec{v_1}$ the coefficient of $O_1B_1$ will be

$$
\frac{d(m + n)}{d^2\sqrt{3}(m^2 + n^2 + mn)} = \frac{m + n}{d\sqrt{3}(m^2 + n^2 + mn)}
$$

We also note that $m^2 + n^2 + mn = l^2$, where $l$ is the length of $OA$.

Doing the calculations for $\vec{v_1}$ and for $\vec{u_1}$ the coefficients of $O_1A_1$ and of $O_1B_1$ all contain the common multiplier $1 / dl^2$.

Since $x\vec{u}+ y\vec{v}$ in $OAB$ maps to $x d\vec{u_1} + y d\vec{v_1}$ in $O_1A_1O_1B_1$ and $d / dl^2 = 1 / l^2$ when it comes to coding we can calculate constant coefficients just once and rather than calculating $\vec{u}$ and $\vec{v}$ as unit vectors calculate them as vectors scaled by $d$, for example

$$
\vec{v} = \frac{(2m + n)O_1B_1 - (m - n)O_1A_1}{l^2\sqrt{3}}
$$

### Further reading

- ##### [A Unified Approach to Geodesic Polyhedra](https://www.researchgate.net/publication/267268539aUnified_Approach_to_Class_I_II_III_Geodesic_Domes)
