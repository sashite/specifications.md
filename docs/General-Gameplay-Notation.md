# General Gameplay Notation <small>Specification and Implementation Guide</small>

A general purpose ASCII-based format for storing patterns defined through the abstract strategy board game rules.

<dl class="dl-horizontal">
  <dt>Created</dt>
  <dd><time datetime="2014-03-08T01:23:45Z">8 March 2014</time></dd>

  <dt>Updated</dt>
  <dd><time datetime="2014-06-09T23:42:34Z">9 June 2014</time></dd>

  <dt>Status</dt>
  <dd>beta</dd>

  <dt>Author</dt>
  <dd>Cyril Wack (<a rel="external" href="//twitter.com/cyri_">@cyri_</a>)</dd>
</dl>

<div class="alert alert-warning">
  <button type="button" class="close" data-dismiss="alert">&times;</button>
  <strong>This is a work in progress!</strong>
  This is a beta document and may be updated at any time.
</div>

* * *

<div class="sub-title">Abstract</div>

This document proposes a format for representing <em>patterns</em> defined through the abstract strategy board <strong>game rules</strong>.

<div class="sub-title">Status of this document</div>

This document is a beta of the GGN specification.

<div class="sub-title">Copyright Notice</div>

The content of this page is licensed under the [Creative Commons Attribution 3.0 License](//creativecommons.org/licenses/by/3.0/), and code samples are licensed under the [Apache 2.0 License](//www.apache.org/licenses/LICENSE-2.0).

* * *

## Introduction

<abbr title="General Gameplay Notation">GGN</abbr> (<q>General Gameplay Notation</q>) is a lightweight, <abbr title="American Standard Code for Information Interchange">ASCII</abbr>-based format that gives a consistent and easy way to represent patterns defined through the abstract strategy board game rules.

Able to describe multidimensional patterns, easy for humans to read and write, and easy for machines to import and export, it is compatible with most abstract strategy board games such as [Draughts](//en.wikipedia.org/wiki/Draughts), [Go](//en.wikipedia.org/wiki/Go_(game)) and the main chess variants, including [<ruby lang="ko">장기<rt lang="en">Janggi</rt></ruby>](//en.wikipedia.org/wiki/Janggi), [<ruby lang="th">หมากรุก<rt lang="en">Makruk</rt></ruby>](//en.wikipedia.org/wiki/Makruk), [<ruby lang="ja">将棋<rt lang="en">Shogi</rt></ruby>](//en.wikipedia.org/wiki/Shogi), [Western](//en.wikipedia.org/wiki/Chess), [<ruby lang="zh">象棋<rt lang="en">Xiangqi</rt></ruby>](//en.wikipedia.org/wiki/Xiangqi).  These properties make GGN an ideal data-interchange format for storing patterns of most actors from abstract strategy board games.

### Notational conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](//tools.ietf.org/html/rfc2119).

## Specification goals

A specification for a general gameplay notation MUST observe the following criteria:

1. The details of the system MUST be publicly available and free of unnecessary complexity.
2. The details of the system MUST be non-proprietary so that users and software developers are unrestricted by concerns about infringing on intellectual property rights.
3. The system MUST work for a variety of programs.  The format SHOULD be such that it can be used by abstract strategy board game database programs, abstract strategy board game server programs, and abstract strategy board game playing programs without being unnecessarily specific to any particular application class.
4. The system MUST handle abstract strategy board games which are playable by multiple sides.
5. The system MUST be easily expandable and scalable.  Examples: new actors, new games, new moves, new rules, etc.
  * The system MUST be cross-variants.
    It SHOULD be able to represent patterns of actors where sides MAY play
    different abstract strategy board games such as Janggi, Go, Draughts, Shogi, Western, Xiangqi.
  * The system MUST handle pattern of move of boards of any dimension; e.g., 2D, 3D.
  * The system MUST handle pattern of move of boards of any size; e.g., 8x8, 9x9, 9x9x9.
  * The system MUST be able to fully describe actor patterns according to
    a concerned Laws of Chess and the given chessboard structure.
6. Finally, the system SHOULD handle the same kinds and amounts of data that are already handled by existing chess software and by print media.

## How to serve GGN

The use of the file suffix "`.ggn`" is RECOMMENDED for files containing GGN data.

When serving GGN over HTTP, the media type "`application/vnd.ggn`" is RECOMMENDED.

## <span id="resource">Notation for gameplays</span>

The structure of move instances can be unambiguously described with a pattern.

<div class="sub-title">Resource representation</div>

The <abbr title="Backus–Naur Form">BNF</abbr> structure below shows the format of the resource:

    <Gameplay>                  ::= <Pattern> '.'
                                  | <Pattern> '. ' <Gameplay>

    <Gameplay Into Base64>      ::= <RFC 4648's Base64-encoded version>

    <Pattern>                   ::= <Ability>
                                  | <Ability> '; ' <Pattern>

    <Ability>                   ::= <Subject> '^' <Verb> '=' <Object>

    <Subject>                   ::= <...Ally?> '<' <Actor> '>' <State>
    <...Ally?>                  ::= <Boolean>
                                  | <Null>
    <Actor>                     ::= <Self>
                                  | <Gameplay Into Base64>
    <State>                     ::= <...Last Moved Actor?> '&' <...Previous Moves Counter>
    <...Last Moved Actor?>      ::= <Boolean>
                                  | <Null>
    <...Previous Moves Counter> ::= <Unsigned Integer>
                                  | '_'

    <Verb>                  ::= <Name> '[' <Direction> ']' <...Maximum Magnitude> '/' <Required?>
    <Name>                  ::= 'capture'
                              | 'remove'
                              | 'shift'
    <Direction>             ::= <Integer>
                              | <Integer> ',' <Direction>
    <...Maximum Magnitude>  ::= <Unsigned Integer Excluding Zero>
                              | '_'
    <Required?>             ::= <Boolean>

    <Object>                  ::= <Square> '~' <Square> '%' <Promotable Into Actors>
    <Square>                  ::= <...Attacked?> '@' <...Occupied!> '+' <Area>
    <...Attacked?>            ::= <Boolean>
                                | <Null>
    <...Occupied!>            ::= <Subject>
                                | <Boolean>
                                | <Null>
                                | <Relationship>
    <Relationship>            ::= 'an_ally_actor'
                                | 'an_enemy_actor'
    <Area>                    ::= 'all'
                                | 'furthest_one-third'
                                | 'furthest_rank'
                                | 'nearest_two-thirds'
                                | 'palace'
    <Promotable Into Actors>  ::= <Self>
                                | <Gameplay Into Base64>
                                | <Gameplay Into Base64> ',' <Promotable Into Actors>

    <Integer>                         ::= <Negative Integer>
                                        | <Unsigned Integer>
    <Negative Integer>                ::= '-' <Unsigned Integer Excluding Zero>
    <Unsigned Integer>                ::= <Zero>
                                        | <Unsigned Integer Excluding Zero>
    <Unsigned Integer Excluding Zero> ::= <Digit Excluding Zero>
                                        | <Unsigned Integer Excluding Zero> <Digit>
    <Digit>                           ::= <Digit Excluding Zero>
                                        | <Zero>
    <Digit Excluding Zero>            ::= '1'
                                        | '2'
                                        | '3'
                                        | '4'
                                        | '5'
                                        | '6'
                                        | '7'
                                        | '8'
                                        | '9'
    <Boolean>                         ::= 'f'
                                        | 't'
    <Null>                            ::= '_'
    <Self>                            ::= 'self'
    <Zero>                            ::= '0'

### Canonical gameplay hash coding

The gameplay of actors can be named by a unique identifier, the <abbr title="Canonical Gameplay Hash">CGH</abbr> (<q>Canonical Gameplay Hash</q>).

This unique identifier is computed from the SHA1 of the ascendant list in alphabetical order of the patterns of an actor.

## Example

The GGN of Western and Xiangqi Rooks on a two-dimensional board is the same:

    t<self>_&_^remove[-1,0]1/t=_@f+all~_@an_enemy_actor+all%self. \
    t<self>_&_^remove[0,-1]1/t=_@f+all~_@an_enemy_actor+all%self. \
    t<self>_&_^remove[0,1]1/t=_@f+all~_@an_enemy_actor+all%self. \
    t<self>_&_^remove[1,0]1/t=_@f+all~_@an_enemy_actor+all%self. \
    t<self>_&_^shift[-1,0]_/t=_@f+all~_@f+all%self. \
    t<self>_&_^shift[-1,0]_/t=_@f+all~_@f+all%self; t<self>_&_^remove[-1,0]1/t=_@f+all~_@an_enemy_actor+all%self. \
    t<self>_&_^shift[0,-1]_/t=_@f+all~_@f+all%self. \
    t<self>_&_^shift[0,-1]_/t=_@f+all~_@f+all%self; t<self>_&_^remove[0,-1]1/t=_@f+all~_@an_enemy_actor+all%self. \
    t<self>_&_^shift[0,1]_/t=_@f+all~_@f+all%self. \
    t<self>_&_^shift[0,1]_/t=_@f+all~_@f+all%self; t<self>_&_^remove[0,1]1/t=_@f+all~_@an_enemy_actor+all%self. \
    t<self>_&_^shift[1,0]_/t=_@f+all~_@f+all%self. \
    t<self>_&_^shift[1,0]_/t=_@f+all~_@f+all%self; t<self>_&_^remove[1,0]1/t=_@f+all~_@an_enemy_actor+all%self.

Its JSON representation could be:

    [
      [
        {
          "subject": {
            "...ally?": true,
            "actor": "self",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "shift",
            "vector": {"...maximum_magnitude": null, "direction": [1,0]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        }
      ],



      [
        {
          "subject": {
            "...ally?": true,
            "actor": "self",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "shift",
            "vector": {"...maximum_magnitude": null, "direction": [-1,0]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        }
      ],



      [
        {
          "subject": {
            "...ally?": true,
            "actor": "self",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "shift",
            "vector": {"...maximum_magnitude": null, "direction": [0,1]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        }
      ],



      [
        {
          "subject": {
            "...ally?": true,
            "actor": "self",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "shift",
            "vector": {"...maximum_magnitude": null, "direction": [0,-1]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        }
      ],



      [
        {
          "subject": {
            "...ally?": true,
            "actor": "self",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "remove",
            "vector": {"...maximum_magnitude": 1, "direction": [1,0]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": "an_enemy_actor",
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        }
      ],



      [
        {
          "subject": {
            "...ally?": true,
            "actor": "self",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "shift",
            "vector": {"...maximum_magnitude": null, "direction": [1,0]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        },
        {
          "subject": {
            "...ally?": true,
            "actor": "self#instance",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "remove",
            "vector": {"...maximum_magnitude": 1, "direction": [1,0]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": "an_enemy_actor",
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        }
      ],



      [
        {
          "subject": {
            "...ally?": true,
            "actor": "self",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "remove",
            "vector": {"...maximum_magnitude": 1, "direction": [-1,0]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": "an_enemy_actor",
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        }
      ],



      [
        {
          "subject": {
            "...ally?": true,
            "actor": "self",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "shift",
            "vector": {"...maximum_magnitude": null, "direction": [-1,0]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        },
        {
          "subject": {
            "...ally?": true,
            "actor": "self#instance",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "remove",
            "vector": {"...maximum_magnitude": 1, "direction": [-1,0]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": "an_enemy_actor",
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        }
      ],



      [
        {
          "subject": {
            "...ally?": true,
            "actor": "self",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "remove",
            "vector": {"...maximum_magnitude": 1, "direction": [0,1]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": "an_enemy_actor",
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        }
      ],



      [
        {
          "subject": {
            "...ally?": true,
            "actor": "self",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "shift",
            "vector": {"...maximum_magnitude": null, "direction": [0,1]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        },
        {
          "subject": {
            "...ally?": true,
            "actor": "self#instance",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "remove",
            "vector": {"...maximum_magnitude": 1, "direction": [0,1]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": "an_enemy_actor",
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        }
      ],



      [
        {
          "subject": {
            "...ally?": true,
            "actor": "self",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "remove",
            "vector": {"...maximum_magnitude": 1, "direction": [0,-1]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": "an_enemy_actor",
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        }
      ],



      [
        {
          "subject": {
            "...ally?": true,
            "actor": "self",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "shift",
            "vector": {"...maximum_magnitude": null, "direction": [0,-1]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        },
        {
          "subject": {
            "...ally?": true,
            "actor": "self#instance",
            "state": {
              "...last_moved_actor?": null,
              "...previous_moves_counter": null
            }
          },

          "verb": {
            "name": "remove",
            "vector": {"...maximum_magnitude": 1, "direction": [0,-1]}
          },

          "object": {
            "src_square": {
              "...attacked?": null,
              "...occupied!": false,
              "area": "all"
            },
            "dst_square": {
              "...attacked?": null,
              "...occupied!": "an_enemy_actor",
              "area": "all"
            },
            "promotable_into_actors": ["self"]
          }
        }
      ]
    ]

## Informative References

This work is influenced by several documents.

* [Portable Action Notation](Portable-Action-Notation)