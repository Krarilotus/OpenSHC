# Stockpile footprint cleanup

`TileMapState::clearStockpileFootprintTiles` (`0x004FAF70` in Crusader 1.41)
clears the nine walkable tiles belonging to a stockpile. The four building
parts are handled elsewhere.

For each entry in `TerrainDefinedData::StockpilePathableOffsets`, it:

1. Clears the tile's `0x102` logic bits and restores its default height.
2. Reads the building index from `AlphaGFXLayer` and checks that building's
   `noRubble` field. Zero clears `BuildingWasLayer`; nonzero sets bit `0x4000`
   in `MiscDisplayLayer` and preserves `BuildingWasLayer`.
3. Clears `AlphaGFXLayer` after reading the building reference.

The map layers belong to the supplied `TileMapState` instance. Building
metadata, terrain offsets and row translation come from the corresponding
global game structures.

## Placement

`placeKeep` (`0x005146D0`) creates a starting stockpile through `placeStockpile`
(`0x00508540`). That call has no human/AI filter.

The stockpile branch of `checkBuildingCanBePlacedHere` (`0x005037B0`) looks up
the supplied owner's stockpile. The first stockpile needs no adjacent existing
stockpile. Further placement requires capacity and adjacency to an owned
stockpile, in addition to the earlier terrain and placement checks.
`AIVState::aiPlaceAIVBuilding` (`0x004ED410`) uses the shared placement path.
