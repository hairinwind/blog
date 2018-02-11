# arangodb 

## AQL filter => where 
filter in AQL equals where in SQL
```
FOR airport IN airports
	FILTER airport.state == "CA"
	RETURN airport
```

## AQL collect => group by
collect in AQL equals group by in SQL
```
FOR airport IN airports
	COLLECT state = airport.state
	WITH COUNT INTO counter
	RETURN {state, counter}	
```

## Operator
https://docs.arangodb.com/3.0/AQL/Operators.html

- == equality
- != inequality
- < less than
- <= less or equal
- > greater than
- >= greater or equal
- IN test if a value is contained in an array
- NOT IN test if a value is not contained in an array
- LIKE tests if a string value matches a pattern
- =~ tests if a string value matches a regular expression
- !~ tests if a string value does not match a regular expression


## graph query
OUTBOUND is equlaized to FILTER xyz._from.
```
FOR airport IN OUTBOUND 'airports/LAX' flights
	RETURN DISTINCT airport
```
same as 
```
For flight in flights
	filter flight._from == 'airports/LAX'
	return distinct flight._to
```

## Breadth First option: bfs
```
FOR v IN 1..10 OUTBOUND 'verts/S' edges
	OPTIONS {bfs: true}
	FILTER v._key == 'F'
	LIMIT 1
	RETURN v
```

## shortest path
```
FOR v IN OUTBOUND
	SHORTEST_PATH 'airports/BIS'
	TO 'airports/JFK' flights
	RETURN v
```

## Pattern Matching
Q: I want to find the connection between BIS and JFK with the lowest total travel time.
```
FOR v, e, p IN 2 OUTBOUND 'airports/BIS' flights
	FILTER v._id == 'airports/JFK'
	FILTER p.edges[*].Month ALL == 1
	FILTER p.edges[*].DayofMonth ALL == 1
	FILTER DATE_ADD(p.edges[0].ArrTimeUTC, 20, 'minutes') < p.edges[1].DepTimeUTC
	LET flightTime = DATE_DIFF(p.edges[0].DepTimeUTC, p.edges[1].ArrTimeUTC, 'i')
	SORT flightTime ASC
	LIMIT 5
	RETURN { flight: p, time: flightTime }
```

