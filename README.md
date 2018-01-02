# nape-heaps

Adding [nape](https://github.com/deltaluca/nape)'s debug draw to [heaps](https://github.com/HeapsIO/heaps), basically just a quick hack right now

Example:
```haxe
import hxd.App;
import hxd.Stage;
import hxd.Event;
import hxd.Key;

import h2d.Graphics;
import h2d.Sprite;
import h2d.Text;

import nape.geom.Vec2;
import nape.phys.Body;
import nape.phys.BodyType;
import nape.shape.Circle;
import nape.shape.Polygon;
import nape.space.Space;
import nape.util.GraphicsDebug;

/**
 *  Running a test
 *  
 *  Create wireframe physics world from nape in heaps
 *  
 *  TODO: Run physics loop on a separte timer
 */
class Main extends App {

    var pg : Graphics;
    var spr : Sprite;
    var tf : Text;

    // Nape
    var space : Space;
    var debug : GraphicsDebug;

    // Init
    override function init() {
        trace('Init!');

        var w = s2d.width;
        var h = s2d.height;
        
        var g = new Graphics(s2d);
        debug = new GraphicsDebug(g, w, h, 0x000000);
        debug.thickness = 1;
        debug.drawConstraints = true;

        // Setup a simple nape world
        space = new Space(Vec2.weak(0, 600));

        // From basic simulation
        var floor = new Body(BodyType.STATIC);
        floor.shapes.add(new Polygon(Polygon.rect(50, (h - 50), (w - 100), 1)));
        floor.space = space;

        for (i in 0...16) {
            var box = new Body(BodyType.DYNAMIC);
            box.shapes.add(new Polygon(Polygon.box(16, 32)));
            box.position.setxy((w / 2), ((h - 50) - 32 * (i + 0.5)));
            box.space = space;
        }

        var ball = new Body(BodyType.DYNAMIC);
        ball.shapes.add(new Circle(50));
        ball.position.setxy(50, h / 2);
        ball.angularVel = 10;
        ball.space = space;

        // Set main event handler
        Stage.getInstance().addEventTarget(onEvent);
    }

    // if we the window has been resized
    override function onResize() {

    }

    // Handle inputs
    function onEvent( e : Event ) {
        switch( e.kind ) {
            case EKeyDown: switch( e.keyCode ) {
                case Key.ESCAPE: hxd.System.exit();
                default:
            }
            default:
        }
    }

    // Main loop
    override function update( dt : Float ) {
        space.step(1 / 60);

        debug.clear();
        debug.draw(space);
        debug.flush();
    }

    // Entry point
    static function main() {
        hxd.Res.initEmbed();
        new Main();
    }

}
```