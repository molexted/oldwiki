Place your secret RE voodoo incantations in good hands that won't tell anyone else.

Undocumented commands listed are not well understood; please document them if you know what they do

### Edit Commands

`/dtoutline 0/1 renders all non-occluded triangles `
`/testpvs `<int>` tests number of potentially viewable surfaces `
`/wireframe 0/1 renders everything in wireframe, with wires the color of the texture inside the square`
`/entediting 0/1 determines if entities get selected, useful to texture underneath/around mapmodels`
`/showmat 0/1 shows materials if enabled, allows selection of geometry behind materials if disabled`
`/maxmerge 0/1 sets the cube size where the remipper will end its geometry optimization; higher numbers lower the geometry quantity at the expense of poorer occlusion`
`/floatspeed `<int>` sets the editing and spectating fly speed`
`/lerpangle`
`/lerpsubdiv`
`/lerpsubdivsize`
`/lerptjoins`

### Gameplay commands

`/teamalphaname `<string>` sets team alpha's name to `<string>`; default is 'alpha' (of course)`
`/teamomeganame `<string>` sets team omega's name to `<string>`; default is 'omega' `
`/teamneutralname `<string>` sets team neutral's (ffa) name to `<string>`; default is 'neutral' `
`/teamalphacolour `<hex>` sets alpha team colour to 0xRRGGBB`
`/teamomegacolour `<hex>` sets omega team colour to 0xRRGGBB`
`/teamneutralcolour `<hex>` sets neutral (ffa) team colour to 0xRRGGBB (this is what gives the ACE orange/green arenas their colours)`

`/gamepaused 0/1 determines if the game is currently paused; note that players can still rotate their FOV though their motion and ability to shoot are obviously disabled`
`/gamespeed `<int>` determines pace of game; 100 is default`

`/ffa `<map>` starts a ffa-deathmatch on `<map>
`/insta `<map>` | /instagib `<map>` starts an insta-deathmatch on `<map>
`/duel `<map>` starts a ffa-duel-hard-basic deathmatch on `<map>
`/ctf `<map>` starts a ctf match on `<map>
`/bb `<map>` | /bomber `<map>` starts a bomber-ball match on `<map>
`/dac `<map>` | /defend `<map>` starts a defend-and-control match on `<map>
`/race `<map>` starts a race match on `<map>

### Misc commands

`/gpuskel 0/1 appears to be a gpu compatibility setting; on HD 7570 (Terascale 2) on r600/ mesa 17 leaving it enabled led to 10% better fps than disabling it; YMMV`
