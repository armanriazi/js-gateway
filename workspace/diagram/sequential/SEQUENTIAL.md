
## Index of Sequential Diagram

* [Diagram with notes](./)
  * [aiServices_ctrl](./Sequential_Diagram_Auth_noted.md)
* [Diagram without notes](./)
  * [aiServices_ctrl](./Sequential_Diagram_Auth.md)
  
## Sequential

In some diagrams, function names may appear. This part of functions can be converted into a status/activity diagram, but to maintain the integrity of this part of the sequential diagram, it has not been removed. This is because it was intended for other entities to be visible alongside the calls. Therefore, we have determined our standard sequential diagram for Auth and are aware of it. For example, OpenConnect also uses functions specifically and sequentially in its explanatory sections.

### Shortforms

R: Repeater, G: Get, P: Post, O: Object

For example, 'R' in the auth class, we have a method called check, which is called and repeated at the beginning of all other methods.

