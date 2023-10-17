## Formalización de Matemática en Asistentes de Prueba

# Formalización de la caracterización por subgrafos inducidos prohibidos de los grafos split en Lean

### Introducción

Una clase de grafos $\mathcal{G}$ se dice **hereditaria** si es cerrada por subgrafos inducidos (es decir, si todo subgrafo inducido de un grafo de $\mathcal{G}$ también pertenece a $\mathcal{G}$). Si $\mathcal{G}$ es un clase de grafos hereditaria entonces admite una caracterización por subgrafos inducidos prohibidos. Esto es, es posible dar una familia $\mathcal{F}$ de grafos no pertenecientes a la clase $\mathcal{G}$ para, luego, decir que un grafo está en $\mathcal{G}$ si y solo si no contiene un subgrafo inducido isomorfo a un grafo de $\mathcal{F}$.