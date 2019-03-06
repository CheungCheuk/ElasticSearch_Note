[TOC]

------

## `方法名` 对应 `Elasticsearch` 查询

#### AND

##### findByNameAndPrice

```json
{"bool" : 
 	{"must" : 
         [ 
            {"field" : {"name" : "?"}}, 
         	{"field" : {"price" : "?"}} 
         ]
    }
}
```

#### OR

##### findByNameOrPrice

```json
{"bool" : 
 	{"should" : 
     	[ 
            {"field" : {"name" : "?"}}, 
            {"field" : {"price" : "?"}} 
        ]
    }
}
```

#### IS

##### findByName

```json
{"bool" : 
 	{"must" : 
     	{"field" : {"name" : "?"}
        }
    }
}
```

#### NOT

##### findByNameNot

```json
{"bool" : 
 	{"must_not" : 
     	{"field" : {"name" : "?"}
        }
    }
}
```

#### LIKE

##### findByNameLike

```json
{"bool" : 
 	{"must" : 
     	{"field" : 
         	{"name" : 
             	{"query" : "?*",
                 "analyze_wildcard" : true}
            }
        }
    }
}
```

#### IN

##### findByNameIn(Collection<String>names)

```json
{"bool" : 
 	{"must" : 
     	{"bool" : 
         	{"should" : 
             	[ 
                    {"field" : {"name" : "?"}}, 
                 	{"field" : {"name" : "?"}} 
                ]
            }
        }
    }
}
```

#### NOT IN

##### findByNameNotIn(Collection<String>names)

```json
{"bool" : 
 	{"must_not" : 
     	{"bool" : 
         	{"should" : 
             	{"field" : {"name" : "?"}
                }
            }
        }
    }
}
```

#### ORDER BY

##### findByAvailableTrueOrderByNameDesc

```json
{"sort" : 
 	[
        { "name" : 
         	{"order" : "desc"} 
        }
    ],
 "bool" : 
 	{"must" : 
     	{"field" : {"available" : true}
        }
    }
}
```

###### 参考来源

- [Using @Query Annotation](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#elasticsearch.query-methods.at-query)