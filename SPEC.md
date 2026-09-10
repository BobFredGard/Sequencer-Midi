# Sequencer 4 - Specification

## Project Overview
- **Project Name**: Sequencer 4
- **Type**: Web-based MIDI Sequencer Application
- **Core Functionality**: A vertical MIDI sequencer with playlist management, grid-based event editing, and MIDI I/O integration
- **Target Users**: Musicians and producers who need a simple, visual MIDI sequencer

## UI/UX Specification

### Layout Structure
- **Overall**: Two-column layout with sidebar (playlist) and main content (sequencer)
- **Left Sidebar** (250px): Playlist panel with song list
- **Right Panel**: Sequence grid with transport controls above
- **Top Control Bar**: Song name, BPM, measure count, subdivision selector, event details

### Responsive Design
- **Minimum Width**: 1024px
- **Fluid Grid**: Horizontal scrolling for >100 measures if needed
- **Vertical Scroll**: Independent scrolling in playlist and grid

### Visual Design (OS X-like Modern)
- **Color Palette**:
  - Background: rgba(30, 30, 35, 0.95) - dark translucent
  - Panel Background: rgba(50, 50, 60, 0.9)
  - Accent: #007AFF (OS X blue)
  - Text Primary: #FFFFFF
  - Text Secondary: #8E8E93
  - Grid Lines: rgba(255, 255, 255, 0.1)
  - Measure Numbers: #8E8E93
  - Beat Markers: rgba(255, 255, 255, 0.3)
  - Event Color: #FF9500 (orange for visibility)
  - Countdown Measures (1-2): rgba(60, 60, 70, 0.5) - grayed
  - Active Measure: rgba(0, 122, 255, 0.3)

- **Typography**:
  - Font Family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif
  - Headings: 14px semibold
  - Body: 12px regular
  - Small: 10px

- **Effects**:
  - Backdrop blur: 20px
  - Border radius: 8px (panels), 4px (buttons)
  - Box shadows: 0 4px 20px rgba(0, 0, 0, 0.3)
  - Transitions: 0.2s ease

### Components

1. **Sidebar Playlist**
   - Song list items (draggable for reorder)
   - Add song button
   - Delete song button (with confirmation)
   - Import/Export playlist buttons

2. **Top Control Bar**
   - Song name input (editable)
   - BPM input (number, range 20-300)
   - Measure count input (number, range 10-200)
   - Subdivision dropdown (4, 8, 16)
   - Event details display (when selected)
   - Event value slider/input

3. **Transport Controls**
   - Play/Pause button
   - Stop button
   - Reset button
   - LED indicator for playback status

4. **Sequence Grid**
   - Vertical layout (measures = rows)
   - Horizontal: subdivisions per measure
   - Row headers: measure numbers (1-100)
   - Column headers: beat/subdivision markers
   - Events: vertical bars spanning full measure height
   - Playhead: blinking line at current position
   - Countdown zone: measures 1-2 grayed

5. **MIDI Controls**
   - MIDI IN indicator
   - MIDI OUT selector
   - Last received note display

## Functionality Specification

### Core Features

1. **Playlist Management**
   - Create new song
   - Rename song
   - Delete song (with confirmation)
   - Reorder songs via drag-and-drop
   - Persist to localStorage

2. **Sequence Grid**
   - 100 measures by default (configurable 10-200)
   - Subdivisions: 4, 8, or 16 per measure
   - Each measure = one row
   - Measures 1-2: countdown zone (grayed)
   - Playback starts at measure 3 after 5-click countdown
   - Events display as vertical bars at their position

3. **Event System**
   - Event data: channel (1-16), CC code (0-127), value (0-127)
   - Create: Click on grid or MIDI input
   - Edit: Select event, modify in control bar
   - Move: Drag event (snaps to nearest subdivision)
   - Delete: ALT + click

4. **Playback System**
   - 5-click countdown (uniform clicks) before measure 3
   - Click tempo based on BPM
   - Playhead moves through grid
   - Events trigger MIDI OUT on beat

5. **MIDI Integration**
   - MIDI IN: Receive CC messages to create events
   - MIDI OUT: Send CC messages during playback
   - MIDI device selection

6. **Persistence**
   - Auto-save current state to localStorage
   - Export playlist to JSON file
   - Import playlist from JSON file

### User Interactions

1. **Adding Event**
   - Mouse: Click at position creates event at snapped location
   - MIDI: CC received creates event at current playhead

2. **Editing Event**
   - Click to select
   - Modify channel/CC/value in control bar

3. **Moving Event**
   - Drag selected event
   - Snaps to nearest beat/subdivision

4. **Deleting Event**
   - ALT + click on event

### Data Structure

```javascript
Song = {
  id: string,
  name: string,
  bpm: number,
  measures: number,
  subdivision: number,
  events: Event[]
}

Event = {
  id: string,
  measure: number,
  subdivision: number,
  channel: number,
  cc: number,
  value: number
}

Playlist = {
  songs: Song[],
  activeSongId: string,
  currentIndex: number
}
```

## Acceptance Criteria

1. ✓ Playlist displays songs with reorder capability
2. ✓ Grid shows 100 measures vertically with scroll
3. ✓ Measures 1-2 appear grayed
4. ✓ 5-click countdown plays before measure 3
5. ✓ Events snap to beat/subdivision grid
6. ✓ Events editable via control bar (no modal)
7. ✓ Events deletable with ALT+click
8. ✓ Events movable with drag
9. ✓ MIDI IN creates events
10. ✓ MIDI OUT triggers events during playback
11. ✓ Save/load individual songs
12. ✓ Export/import full playlist
13. ✓ localStorage autosave
14. ✓ OS X-like modern interface with transparency