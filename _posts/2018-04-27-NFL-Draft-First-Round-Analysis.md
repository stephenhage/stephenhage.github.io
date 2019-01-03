---
layout: post
title: "NFL Draft 2018: First Round Review"
date: 2018-04-27 10:21:50 -0600
categories: articles
tags: sports sportsanalytics nfldraft python
---


# First Round Selections Are In!

Yesterday I wrote about my predictions for the draft. As a recap, I scraped in the neighborhood of 60 mock drafts posted to /r/NFL_Draft and assembled them. I created distributions for each player listed in more than three drafts (which eliminated a lot of the fringe guys that might be late-round selections). 

The first round saw a lot of trades, which introduced a fair amount of chaos to the system. Here's the list of actual picks from last night:

| Pick | Team | Player | Position | School |
|:--------|:----------|:------------|:------------|----------:|
| 1 | Cleveland Browns | Baker Mayfield | QB | Oklahoma |
| 2 | New York Giants | Saquon Barkley | RB | Penn State |
| 3 | New York Jets | Sam Darnold | QB | USC |
| 4 | Cleveland Browns | Denzel Ward | CB | Ohio State |
| 5 | Denver Broncos | Bradley Chubb | EDGE | NC State |
| 6 | Indianapolis Colts | Quenton Nelson | G | Notre Dame |
| 7 | Buffalo Bills | Josh Allen | QB | Wyoming |
| 8 | Chicago Bears | Roquan Smith | LB | Georgia |
| 9 | San Francisco 49ers | Mike McGlinchey | OT | Notre Dame |
| 10 | Arizona Cardinals | Josh Rosen | QB | UCLA |
| 11 | Miami Dolphins | Minkah Fitzpatrick | S | Alabama |
| 12 | Tampa Bay Buccaneers | Vita Vea | DT | Washington |
| 13 | Washington Redskins | Da'Ron Payne | DT | Alabama |
| 14 | New Orleans Saints | Marcus Davenport | EDGE | UTSA |
| 15 | Oakland Raiders | Kolton Miller | OT | UCLA |
| 16 | Buffalo Bills | Tremaine Edmunds | LB | Virginia Tech |
| 17 | Los Angeles Chargers | Derwin James | SS | FSU |
| 18 | Green Bay Packers | Jaire Alexander | CB | Louisville |
| 19 | Dallas Cowboys |  Leighton Vander Esch | LB | Boise State |
| 20 | Detroit Lions | Frank Ragnow | OC | Arkansas |
| 21 | Cincinnati Bengals | Billy Price | OC | Ohio State |
| 22 | Tennessee Titans | Rashaan Evans | LB | Alabama |
| 23 | New England Patriots | Isaiah Wynn | G | Georgia |
| 24 | Carolina Panthers | D.J. Moore | WR | Maryland |
| 25 | Baltimore Ravens | Hayden Hurst | TE | South Carolina |
| 26 | Atlanta Falcons | Calvin Ridley | WR | Alabama |
| 27 | Seattle Seahawks | Rashaad Penny | RB | San Diego St |
| 28 | Pittsburgh Steelers | Terrell Edmunds | S | Virginia Tech |
| 29 | Jacksonville Jaguars | Taven Bryan | DT | Florida |
| 30 | Minnesota Vikings | Mike Hughes | CB | Central Florida |
| 31 | New England Patriots | Sony Michel | RB | Georgia |
| 32 | Baltimore Ravens | Lamar Jackson | QB | Louisville |

I took each player that was picked before the lower bound in the confidence interval and labeled him as a reach. Likewise I labeled anyone picked after the upper bound as "player fell". The resulting analysis? The confidence intervals didn't really hold. Twelve of thirty-two picks were outside their bounds.

### Reaches

* Baker Mayfield, expected to go 2-14, went number 1 to the Browns
* Denzel Ward, expected to go between 7-21, went 4th to the Browns; both he and Mayfield were surprise picks in almost every draft analysis I've read
* Mike McGlinchey, expected to go 23-38, went 9th to the 49ers
* Da'Ron Payne, expected to go between 14-38, went 13th to the Redskins
* Kolton Miller, expected to go 22-76, went 15th to the Raiders
* Frank Ragnow, expected to go 29-74, went 20th to the Lions
* Hayden Hurst, expected to go 27-123, went 25th to the Ravens
* Rashaad Penny (the reach of the night), expected to go 50-106, went 27th to the Seahawks
* Terrell Edmunds, expected to go 55-143, went 28th to the Steelers

### Players that Fell

* Sam Darnold, expected to go 1 or 2, went number 3 to the Jets (there was an over confidence in him across the board)
* Josh Rosen, expected to go 1-7, fell to 10 where the Cardinals traded up to draft him
* Tremaine Edmunds, expected to go 6-15, fell to 16 where the Bills traded up to get him

## So what's left?
Only two players with a first round "upper bound" fell, suggesting that there are two consensus first round picks left on the board. They are Harold Landry (Pass Rusher from Boston College) and Will Hernandez (Guard from UTEP). Here's the the list of my best players left:

 | First Last | Lower Bound | Upper bound | School | Position | Predicted Pick | 
 |:--------|:-----------|:-----------|:-----------|:-----------|----------:|
 | Harold Landry | 8 | 30 | Boston College | DE/OLB | 14 | 
 | Connor Williams | 7.5 | 34.1 | UT | OT | 17 | 
 | Josh Jackson | 10.5 | 35.1 | Iowa | CB | 19 | 
 | Will Hernandez | 15 | 32.2 | UTEP | OG | 22 | 
 | Maurice Hurst | 17 | 49.5 | Michigan | DT | 26 | 
 | Derrius Guice | 13 | 38 | LSU | RB | 27 | 
 | Isaiah Oliver | 11.2 | 45 | Colorado | CB | 28 | 
 | James Daniels | 16.6 | 48.6 | Iowa | OG/C | 32 | 
 | Sam Hubbard | 17.9 | 58.3 | Ohio State | DE/OLB | 34 | 
 | Courtland Sutton | 13.3 | 59 | SMU | WR | 36 | 
 | Justin Reid | 23.1 | 74.9 | Stanford | S | 37 | 
 | Braden Smith | 25.1 | 68.6 | Auburn | OG | 38 | 
 | Malik Jefferson | 28 | 63.6 | Texas | LB | 39 | 
 | Ronnie Harrison | 24 | 80.4 | Alabama | S | 40 | 
 | Arden Key | 21.2 | 76.4 | LSU | DE/OLB | 41 | 
 | Mason Rudolph | 8.7 | 77.6 | Oklahoma State | QB | 43 | 
 | Carlton Davis | 24 | 65.3 | Auburn | CB | 44 | 
 | Christian Kirk | 19.9 | 79.3 | Texas A&M | WR | 45 | 
 | Tyrell Crosby | 22.1 | 71.8 | Oregon | OG/OT | 46 | 
 | Brian O’Neill | 12 | 88 | Pittsburgh | OT | 47 | 
 | Dallas Goedert | 27 | 82.8 | South Dakota State | TE | 48 | 
 | Mike Gesicki | 17.6 | 80.8 | Penn State | TE | 49 | 
 | Ronald Jones | 32 | 81.2 | USC | RB | 50 | 
 | Harrison Phillips | 31.1 | 87.8 | Stanford | DT | 51 | 
 | Jessie Bates | 28 | 73 | Wake Forest | S | 52 | 
 | James Washington | 23.2 | 82.8 | Oklahoma State | WR | 54 | 
 | Lorenzo Carter | 25 | 108.4 | Georgia | LB/DE | 56 | 
 | Martinas Rankin | 36 | 84.4 | Mississippi State | OL | 57 | 
 | Darius Leonard | 42.8 | 87 | South Carolina State | LB | 59 | 
 | Michael Gallup | 35 | 93.3 | Colorado State | WR | 60 | 
 | Nick Chubb | 48.5 | 98 | Georgia | RB | 61 | 
 | Jemarco Jones | 66 | 66 | Ohio State | OT | 62 | 
 | Josh Sweat | 27 | 115.8 | Florida State | DE | 63 | 
 | Anthony Miller | 38.3 | 94.2 | Memphis | WR | 64 | 
 | Austin Corbett | 37.8 | 128.9 | Nevada | OG | 65 | 
 | Orlando Brown | 14.2 | 107.9 | Oklahoma | OT | 66 | 
 | Donte Jackson | 39.6 | 101.1 | LSU | CB | 67 | 
 | DJ Chark | 26.9 | 108.1 | LSU | WR | 68 | 
 | Mark Andrews | 35.5 | 109.1 | Oklahoma | TE | 69 | 
 | Ogbonnia Okoronkwo | 38.7 | 106 | Oklahoma | DE/OLB | 70 | 
 | DeShon Elliott | 34.1 | 105.1 | Texas | S | 71 | 
 | Tim Settle | 44 | 107.8 | Virginia Tech | DT | 72 | 
 | Chukwuma Okorafor | 35.6 | 106.7 | WMU | OT | 73 | 
 | Rasheem Green | 51 | 111 | USC | EDGE | 75 | 
 | Daniel Pettis | 74 | 74 | Washington | WR | 76 | 
 | Jamarco Jones | 31.5 | 113.2 | Ohio State | OT | 77 | 
 | Dorance Armstrong | 37.8 | 136.4 | Kansas | DE | 78 | 
 | MJ Stewart | 54 | 99.8 | UNC | CB | 79 | 
 | Uchenna Nwosu | 44.1 | 97.2 | USC | DE/OLB | 80 | 
 | Kerryon Johnson | 48.2 | 108.3 | Auburn | RB | 81 | 
 | Rasheed Green | 53 | 95.8 | USC | DE/DT | 82 | 
 | Equanimeous St. | 43.4 | 134.2 | Notre Dame | WR | 83 | 
 | Holton Hill | 50.7 | 119.3 | Texas | CB | 84 | 
 | Jerome Baker | 44.1 | 157.2 | Ohio State | LB | 85 | 
 | Fred Warner | 51.5 | 141 | BYU | LB | 86 | 
 | Anthony Averett | 61 | 120.7 | Alabama | CB | 87 | 
 | Hercules Mata'afa | 27 | 165.4 | Washington St | EDGE | 88 | 
 | Derrick Nnandi | 86 | 86 | FSU | DT | 89 | 
 | Dante Pettis | 47.8 | 144.9 | Washington | WR | 90 | 
 | Kemoko Turay | 59 | 130.5 | Rutgers | OLB/DE | 91 | 
