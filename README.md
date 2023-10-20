## Formalización de Matemática en Asistentes de Prueba

# Formalización de la caracterización por subgrafos inducidos prohibidos de los grafos split en Lean

### Introducción

Una clase de grafos $\mathcal{G}$ se dice _hereditaria_ si es cerrada por subgrafos inducidos (es decir, si todo subgrafo inducido de un grafo de $\mathcal{G}$ también pertenece a $\mathcal{G}$). Si $\mathcal{G}$ es un clase de grafos hereditaria entonces admite una caracterización por subgrafos inducidos prohibidos. Esto es, es posible dar una familia $\mathcal{F}$ de grafos no pertenecientes a la clase $\mathcal{G}$ para, luego, decir que un grafo está en $\mathcal{G}$ si y solo si no contiene un subgrafo inducido isomorfo a un grafo de $\mathcal{F}$.

Si bien la teoría de grafos ha sido formalizada parcialmente en algunos asistentes de prueba como Coq, Isabelle y Lean, y ya se han formalizado algunos resultados presentes en la literatura, aún no se ha realizado la formalización de ningún teorema de caracterización de una clase de grafos por subgrafos inducidos prohibidos. 

El primer objetivo de este proyecto es estudiar la solidez de las definiciones ya formuladas en los distintos asistentes de prueba mencionados anteriormente, con especial énfasis en las definiciones de _grafo_ y _subgrafo_. Dependiendo del éxito en esta tarea, el segundo objetivo es formalizar (de ser posible, en Lean) el teorema de caracterización por subgrafos inducidos prohibidos de los grafos split, siguiendo la demostración en "Algorithmic graph theory and perfect graphs" (Golumbic, 2004). 

## Primera parte

### Definición de _grafo_
En lo que sigue, dado un conjunto $A$, denotamos por $\mathcal{P}(A)$ al conjunto de partes de $A$.

Un _grafo_ $G$ consiste en un conjunto finito $V$, llamado el conjunto de _vértices_, y una relación binaria irreflexiva sobre $V$, llamada la _relación de adyacencia_. La relación de adyacencia puede ser representada o bien como un conjunto $E$ de pares ordenados, o bien como una función 

\[\ady: V\rightarrow \mathcal{P}(V).\] 

Cada par $(v,w)\in E$ es una _arista_ y $\ady(v)$ es el _conjunto de adyacencia_ de $v\in V$. Claramente,

\[(v,w)\in E\hspace{0.5cm}\text{si y solo si}\hspace{0.5cm}w \in \ady(v).\]

En tal caso, decimos que $v$ y $w$ son los _extremos_ de $(v,w)$ y que $v$ y $w$ son _adyacentes_ o _vecinos_.

Observemos que, como la relación es irreflexiva, para cada $v\in V$ se tiene que $(v,v)\notin E$, o equivalentemente, $v\notin \ady(v)$.

### Definición de _isomorfismo de grafos_

Dos grafos $G=(V, E)$ y $G'=(V', E')$ son _isomorfos_, y se escribe $G\cong G'$, si existe una biyección $f:V\rightarrow V'$ tal que, para todo $v,w\in V$, 

\[(v,w)\in E\hspace{0.5cm}\text{si y solo si}\hspace{0.5cm}(f(v),f(w))\in E'.\] 

#### Definición de _grafo no dirigido_

Sea $G=(V, E)$ un grafo. El _reverso_ de $G$ es el grafo $G^{-1}=(V, E^{-1})$, donde 

\[E^{-1}=\{(w,v)\mid (v,w)\in E\}.\] 

La _clausura simétrica_ de $G$ es el grafo $\hat G=(V,\hat E)$, donde 
    
\[\hat E=E\cup E^{-1}.\] 

Un grafo $G=(V, E)$ se dice _no dirigido_ si su relación de adyacencia es simétrica, es decir, si $E=E^{-1}$, o equivalentemente, $E=\hat{E}$.

### Definición de _subgrafo_ y _subgrafo inducido_

Sea $G=(V,E)$ un grafo. Un _subgrafo_ de $G$ es un grafo $H=(V',E')$ tal que $V'\subseteq V$ y $E'\subseteq E$. Dado $A\subseteq V$, el _subgrafo inducido_ por $A$ es el grafo $G_A=(A,E_A)$, donde

\[E_A=\{(v,w)\in E\mid v\in A, w\in A\}.\]

### Otras definiciones de interés

Sea $G=(V, E)$ un grafo no dirigido. El _complemento_ de $G$ es el grafo $\overline G=(V,\overline E)$, donde

\[\overline E=\{(x,y)\in V\times V\mid x\neq y, (x,y)\notin E\}.\]

Un grafo $G=(V,E)$ es _completo_ si $E=\{(v,w)\in V\times V\mid v\neq w\}$.

Denotamos por $K_n$ al grafo completo con $n$ vértices. 

De aquí en adelante, asumiremos que $G=(V,E)$ es un grafo no dirigido. 

Un subconjunto $A\subseteq V$ de $r$ vértices es una _clique_ si induce un subgrafo completo, es decir, si $G_A\cong K_r$. Una clique es _maximal_ si no está contenida estrictamente en ninguna otra clique de $G$. Una clique es _máxima_ si es de cardinalidad máxima. El _número clique_ de $G$, denotado $\omega(G)$, es el número de vértices en una clique máxima de $G$.  

Un _conjunto independiente_ (o _estable_) de $G$ es un subconjunto $X\subseteq V$ de vértices no adyacentes de a pares. El _número de independencia_ de $G$, denotado $\alpha(G)$, es el número de vértices en un conjunto independiente de $G$ de máxima cardinalidad.

Una secuencia de vértices $v_0,v_1,\ldots,v_n,v_0$ es un _ciclo_ de longitud $n+1$ si $(v_{i-1}, v_i)\in E$ para todo $i\in\{1,2,\ldots,n\}$ y $(v_n, v_0)\in E$. Decimos que un ciclo $v_0,v_1,\ldots,v_n,v_0$  es un _ciclo simple_ si $v_i\neq v_j$ para todo $i\neq j$. Un ciclo simple es un _ciclo sin cuerdas_ si para cualquier par de vértices $v_i, v_j$ no consecutivos en el ciclo se tiene que $(v_i,v_j)\notin E$.

Denotamos por $C_n$ al ciclo sin cuerdas con $n$ vértices. 

### Definición de _grafo cordal_

$G$ es un _grafo cordal_ si todo ciclo de longitud estrictamente mayor que 3 tiene una cuerda, esto es, una arista que une dos vértices no consecutivos del ciclo. Equivalentemente, $G$ no contiene un subgrafo inducido isomorfo a $C_n$ para $n>3$. 

### Definición de _grafo split_

A continuación, si $A, B$ y $C$ son conjuntos, escribiremos $C=A+B$ para indicar que $A\cap B=\emptyset$ y $C=A\cup B$.

$G$ es un _grafo split_ si existe una partición $V=S+K$ del conjunto de vértices de $G$ en un conjunto independiente $S$ y una clique $K$. 

No hay restricciones sobre las adyacencias entre vértices de $S$ y vértices de $K$. En general, la partición $V=S+K$ de un grafo split no será única. A su vez, $K$ (resp. $S$) no necesariamente será una clique (resp. un conjunto independiente) maximal. 

\section{Segunda Parte}

\subsection{Resultados previos}

\begin{obs}
    Todo conjunto independiente de $G$ es una clique de $\overline G$ y viceversa.    
\end{obs}

\begin{teo}
    $G$ es un grafo split si y solo si $\overline G$ es un grafo split.
\end{teo}

\begin{teo}[Hammer y Simeone, 1977]
    Sea $G=(V,E)$ un grafo split cuyo conjunto de vértices está particionado en un conjunto estable $S$ y una clique $K$. Entonces vale exactamente una de las siguientes afirmaciones:
    \begin{enumerate}[$($i$)$]
        \item $|S|=\alpha(G)$ y $|K|=\omega(G)$ (en este caso, la partición es única);
        \item $|S|=\alpha(G)$ y $|K|=\omega(G)-1$ (en este caso, existe $x\in S$ tal que $K+\{x\}$ es una clique);
        \item $|S|=\alpha(G)-1$ y $|K|=\omega(G)$ (en este caso, existe $y\in K$ tal que $S+\{y\}$ es un conjunto estable).
    \end{enumerate}
\end{teo}

\begin{figure}
    \centering
    \begin{subfigure}{0.3\textwidth}
        \centering
        \begin{tikzpicture}\tikzstyle{every node}=[normal]
            \node (1) at (0, 1) {};
            \node (2) at (0, -1) {};
            \node (3) at (2, 1) {};
            \node (4) at (2, -1) {};
            \draw (1) to (2);
            \draw (3) to (4);
        \end{tikzpicture}
        \caption*{$2K_2=\overline{C_4}$}
    \end{subfigure}
    \begin{subfigure}{0.3\textwidth}
        \centering
        \begin{tikzpicture}\tikzstyle{every node}=[normal]
            \node (1) at (-1, 0) {};
            \node (2) at (1, 0) {};
            \node (3) at (0, 1) {};
            \node (4) at (0, -1) {};
            \draw (2) to (3);
            \draw (4) to (1);
            \draw (1) to (3);
            \draw (2) to (4);
        \end{tikzpicture}
        \caption*{$C_4$}
    \end{subfigure}
    \begin{subfigure}{0.3\textwidth}
        \centering
        \begin{tikzpicture}\tikzstyle{every node}=[normal]
            \node (1) at (-1, 0) {};
            \node (2) at (1, 0) {};
            \node (3) at (0, 1) {};
            \node (4) at (-0.5, -1) {};
            \node (5) at (0.5, -1) {};
            \draw (2) to (3);
            \draw (4) to (1);
            \draw (1) to (3);
            \draw (4) to (5);
            \draw (5) to (2);
        \end{tikzpicture}
        \caption*{$C_5$}
    \end{subfigure}
    \caption{}
    \label{fig:prohibidos}
\end{figure}

\subsection{Teorema de caracterización de los grafos split}

La clase de los grafos split es un clase hereditaria. Por lo tanto, admite una caracterización por subgrafos inducidos prohibidos. Dicha caracterización fue dada por Földes y Hammer en \cite{MR0505860}. 

\begin{teo}[Földes y Hammer, 1977]\label{teo:principal}
    Sea $G=(V,E)$ un grafo no dirigido. Las siguientes afirmaciones son equivalentes:
    \begin{enumerate}[$($i$)$]
        \item\label{it:1} $G$ es un grafo split;
        \item\label{it:2} $G$ y $\overline G$ son grafos cordales;
        \item\label{it:3} $G$ no contiene un subgrafo inducido isomorfo a $2K_2$, $C_4$ o $C_5$ (Figura \ref{fig:prohibidos}).
    \end{enumerate}
\end{teo}
