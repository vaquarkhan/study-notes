# Week 1

## 1.5 Union Find

* About the "Union find problem"

  * Set of algorithms for sovling the "dynamic connectivity" problem. 
  * Look at "Quick find" and "Quick union"

* Steps to developing a usable algorithm -- scientific approach.

  1. Model the problem -- what are the main components of problem that need to be solved?
  2. Find an algorithm to solve it.
  3. Fast enough? Fits in memory?
  4. If not, figure out why.
  5. Find way to address problem.
  6. Iterate until satisfied.


### Dynamic connectivity

* Dynamic connectivity.


  * Given set of N objects:
    * Find / connected query: is there a path connecting any 2 objects?

      <img src="./images/dynamic-connectivity.png"></img>

    * Assumptions:

      * "Is connected to" is an "equivalence relationship:
        * Reflexive: Node A is connected to itself.

            * |Question| Word to describe equivalence relationship when ``p`` is connected to ``p``?

        * Symmetric: If Node A is connected to Node B, Node B is connected to Node A.
          * This might not be the case in a social network graph, for example.

          * |Question| Word to describe equivalence relationship when ``p`` is connected to ``r`` infers ``r`` is connected to ``p``?

        * Transitive: if Node A is connected to Node B, and Node B is connected to Node C, then Node A is connected to Node C.

          * |Question| Word to describe equivalence relationship when ``p`` is connected to ``r`` and ``r`` is connected to ``s``, then ``p`` is connected to ``s``?

    * Connected components

      * "Maximal set of objects that are mutually connected."

        <img src="./images/connected-components.png"></img>

* Implementing the operations

  * Find query - checks if 2 objects are in the same "component" (referring to above section about "Connected components").
  * Union command - union 2 component objects and use to replace the 2 componets when two nodes get connected.

      <img src="./images/union-find-operations.png"></img>

* Union-find data type

  * Goal: make efficient data structure for union-find.
    * Number of objects ``N`` can be huge.
    * Number of operations ``M`` can be huge.
    * Find queries and union commands may be intermixed.
    * Python example:

      ```
      class UnionFind(object):

          def union(p, q):
              """
              Args:
                  p (int) - node 1 to union.
                  q (int) - node 2 to union.
              """

          def connected(p, q):
              """
              Args:
                  p (int) - node 1 to check if connected.
                  q (int) - node 2 to check if connected.

              Return:
                  bool
              """
      ```
* [Dynamic connectivity client](./code/dynamic_connectivity_client.java)

## Quick Find

* "Eager" algorithm for solving the connectivity problem.
* Data structure
  * Integer array ``id[]`` of size ``N``.
  * ``p`` and ``q`` are connected if and only if (iff) they have the same id.
  * Objects are denoted by their position in the array. Eg object 0 = pos 0.

    ```
    # obj: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 
    ids = [0, 1, 1, 8, 8, 0, 0, 1, 8, 8]

    # component 1: {0, 5, 6}
    # component 2: {1, 2, 7}
    # component 3: {3, 4, 8, 9}
    ```

  * Find: simply check if p and q have same id.

    ```
    def connected(p, q):
        return ids[p] == ids[q]
    ``` 

  * Union: to merge components containing ``p`` and ``q``, change all entries whose id is ``id[p]`` to ``id[q]``.

    ```
    def union(p, q):
        for val, pos in ids:
            if val == ids[p]:
                ids[pos] == ids[q]
    ```

  * [Python implementation](./code/python/quick_find.py)
  * [Java implementation](./code/java/QuickFind.class)

  * Run time:

    * Init: ``N``
    * Union: ``N``
    * Find: ``1``

  * Defects:

    * Union too expensive when processing sequence of ``N`` union command on ``N`` objects: quadratic run time (``N**2``)
    * Quadratic time is "unacceptable" because it doesn't scale: eg if the amount of shit you can store in memory increases, the running time will get slower.

## Quick Union

  * "Lazy" approach: don't do work until we have to.
  * Same data structure as Quick Find but different representation in array.
  * Array represents a set of trees called a "forest".
  * Each element points to their parent, parents point to themselves.

  ```
  # 0   1     5
  #          / \
  #         4   2
  #              \
  #               3
  # Obj: 0, 1, 2, 3, 4, 5
  ids = [0, 1, 5, 2, 5, 5]
  ```

  * Find: check if ``p`` and ``q`` have same root.

  ```
  def get_root(i):
      if self.ids[i] != i:
          return get_root(self.ids[i])
      return i

  def find(p, q):
     return self.get_root(p) == self.get_root(q)
  ```

  * Union: Connect the root of ``p`` to the root of ``q``.

  ```
  def union(p, q):
     p_root = self.get_root(p)
     q_root = self.get_root(q)
     self.ids[p_root] = q_root
  ```

  * Run time:

    * Init: ``N``
    * Union: ``N``
    * Find: ``N``  <  worst case

  * Defects:

    * Find too expensive: could be ``N`` array access in case where finding the root needs to interate over all nodes (linear time).