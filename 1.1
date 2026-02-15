import bpy
import bmesh
from mathutils import Vector
import math
import bpy.utils.previews
import tempfile
import os

# ------------------------------------------------------------
# Preview icons (heatmap)
# ------------------------------------------------------------

preview_collections = {}

def generate_color_icons():

    if "curvature_icons" in preview_collections:
        bpy.utils.previews.remove(preview_collections["curvature_icons"])
        del preview_collections["curvature_icons"]

    pcoll = bpy.utils.previews.new()
    preview_collections["curvature_icons"] = pcoll

    size = 32

    scene = bpy.context.scene
    scale = scene.curvature_scale
    power = scene.curvature_power

    # Weight Paint ramp exacto
    stops = [
        (0.0,  (0.0, 0.0, 1.0)),
        (0.25, (0.0, 1.0, 1.0)),
        (0.5,  (0.0, 1.0, 0.0)),
        (0.75, (1.0, 1.0, 0.0)),
        (1.0,  (1.0, 0.0, 0.0)),
    ]

    def evaluate_ramp(t):
        for i in range(len(stops) - 1):
            t0, c0 = stops[i]
            t1, c1 = stops[i + 1]
            if t0 <= t <= t1:
                f = (t - t0) / (t1 - t0)
                return (
                    c0[0] + f * (c1[0] - c0[0]),
                    c0[1] + f * (c1[1] - c0[1]),
                    c0[2] + f * (c1[2] - c0[2]),
                )
        return stops[-1][1]

    values = [1.0, 0.9, 0.8, 0.7, 0.6, 0.5, 0.4, 0.3, 0.2, 0.1, 0.0]

    for i, factor in enumerate(values):

        # 🔹 Aplicar MISMA transformación que el heatmap
        scaled = min(1.0, factor * scale)
        scaled = pow(scaled, power)
        scaled = max(0.0, min(1.0, scaled))

        r, g, b = evaluate_ramp(scaled)

        image = bpy.data.images.new(f"curv_icon_{i}", width=size, height=size)

        pixels = [r, g, b, 1.0] * (size * size)
        image.pixels = pixels

        temp_path = os.path.join(tempfile.gettempdir(), f"curv_icon_{i}.png")
        image.filepath_raw = temp_path
        image.file_format = 'PNG'
        image.save()

        pcoll.load(f"curv_icon_{i}", temp_path, 'IMAGE')

# ------------------------------------------------------------
# Operador calcular curvatura
# ------------------------------------------------------------

class MESH_OT_calculate_curvature(bpy.types.Operator):
    """Calcula la curvatura visual basada en diferencias angulares de normales"""
    bl_idname = "mesh.calculate_curvature"
    bl_label = "Calcular Curvatura"
    bl_options = {'REGISTER', 'UNDO'}
    
    def execute(self, context):
        obj = context.active_object
        
        if obj is None or obj.type != 'MESH':
            self.report({'ERROR'}, "Selecciona una malla válida")
            return {'CANCELLED'}
        
        scale = context.scene.curvature_scale
        power = context.scene.curvature_power
        
        if "Curvature" not in obj.vertex_groups:
            obj.vertex_groups.new(name="Curvature")
        
        vg = obj.vertex_groups["Curvature"]
        
        bm = bmesh.new()
        bm.from_mesh(obj.data)
        bm.verts.ensure_lookup_table()
        bm.edges.ensure_lookup_table()
        bm.normal_update()
        
        curvatures = []
        
        for vert in bm.verts:
            connected_verts = [e.other_vert(vert) for e in vert.link_edges]
            
            if len(connected_verts) == 0:
                curvatures.append(0.0)
                continue
            
            angle_sum = 0.0
            normal = vert.normal
            
            for other_vert in connected_verts:
                other_normal = other_vert.normal
                dot = max(-1.0, min(1.0, normal.dot(other_normal)))
                angle = math.acos(dot)
                angle_sum += angle
            
            avg_angle = angle_sum / len(connected_verts)
            curvatures.append(avg_angle)
        
        bm.free()
        
        if len(curvatures) > 0:
            max_curv = max(curvatures)
            min_curv = min(curvatures)
            
            context.scene.curvature_min = min_curv
            context.scene.curvature_max = max_curv
            
            for i, curv in enumerate(curvatures):
                if max_curv > min_curv:
                    normalized = (curv - min_curv) / (max_curv - min_curv)
                else:
                    normalized = 0.5
                
                scaled = min(1.0, normalized * scale)
                scaled = pow(scaled, power)
                
                vg.add([i], scaled, 'REPLACE')
        
        obj.data.use_paint_mask_vertex = False
        
        if context.mode != 'PAINT_WEIGHT':
            bpy.ops.object.mode_set(mode='WEIGHT_PAINT')
        
        obj.vertex_groups.active = vg

        # Generar iconos dinámicos
        generate_color_icons()
        
        self.report({'INFO'}, f"Curvatura calculada para {len(curvatures)} vértices")
        return {'FINISHED'}


# ------------------------------------------------------------
# Limpiar curvatura
# ------------------------------------------------------------

class MESH_OT_clear_curvature(bpy.types.Operator):
    bl_idname = "mesh.clear_curvature"
    bl_label = "Limpiar Curvatura"
    bl_options = {'REGISTER', 'UNDO'}
    
    def execute(self, context):
        obj = context.active_object
        
        if obj and obj.type == 'MESH' and "Curvature" in obj.vertex_groups:
            obj.vertex_groups.remove(obj.vertex_groups["Curvature"])
            context.scene.curvature_min = 0.0
            context.scene.curvature_max = 0.0
        
        return {'FINISHED'}


# ------------------------------------------------------------
# Panel escala de colores
# ------------------------------------------------------------

class VIEW3D_PT_curvature_colorbar(bpy.types.Panel):
    bl_label = "Escala de curvatura"
    bl_idname = "VIEW3D_PT_curvature_colorbar"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Curvatura'
    bl_parent_id = "VIEW3D_PT_curvature_analyzer"
    
    def draw(self, context):
        layout = self.layout
        scene = context.scene
        obj = context.active_object
        
        if not (obj and obj.type == 'MESH' and "Curvature" in obj.vertex_groups):
            layout.label(text="Calcula la curvatura primero", icon='INFO')
            return
        
        min_val = scene.curvature_min
        max_val = scene.curvature_max
        
        min_deg = math.degrees(min_val)
        max_deg = math.degrees(max_val)
        range_deg = max_deg - min_deg
        
        box = layout.box()
        col = box.column(align=True)
        col.scale_y = 0.7
        
        values = [1.0, 0.9, 0.8, 0.7, 0.6, 0.5, 0.4, 0.3, 0.2, 0.1, 0.0]
        pcoll = preview_collections.get("curvature_icons")

        for idx, factor in enumerate(values):
            value_deg = min_deg + (range_deg * factor)
            row = col.row(align=True)
            
            sub_color = row.row(align=True)
            sub_color.scale_x = 0.15

            if pcoll and f"curv_icon_{idx}" in pcoll:
                icon = pcoll[f"curv_icon_{idx}"]
                sub_color.label(text=" ", icon_value=icon.icon_id)

            sub_deg = row.row(align=True)
            sub_deg.alignment = 'RIGHT'
            sub_deg.label(text=f"{value_deg:.2f}°")


# ------------------------------------------------------------
# Panel principal
# ------------------------------------------------------------

class VIEW3D_PT_curvature_analyzer(bpy.types.Panel):
    bl_label = "Curvatura de Normales"
    bl_idname = "VIEW3D_PT_curvature_analyzer"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Curvatura'
    
    def draw(self, context):
        layout = self.layout
        scene = context.scene
        obj = context.active_object
        
        box = layout.box()
        if obj and obj.type == 'MESH':
            box.label(text=f"Malla: {obj.name}", icon='MESH_DATA')
            box.label(text=f"Vértices: {len(obj.data.vertices)}")
        else:
            box.label(text="Selecciona una malla", icon='ERROR')
        
        layout.separator()
        
        col = layout.column(align=True)
        col.scale_y = 1.5
        col.operator("mesh.calculate_curvature", icon='SMOOTHCURVE')
        col.operator("mesh.clear_curvature", icon='X')
        
        layout.separator()
        
        box = layout.box()
        box.label(text="Ajustes de Visualización", icon='PREFERENCES')
        
        col = box.column(align=True)
        col.prop(scene, "curvature_scale", slider=True)
        col.prop(scene, "curvature_power", slider=True)


# ------------------------------------------------------------
# Propiedades
# ------------------------------------------------------------

def register_properties():
    bpy.types.Scene.curvature_scale = bpy.props.FloatProperty(
        name="Escala",
        default=1.0,
        min=0.1,
        max=10.0
    )
    
    bpy.types.Scene.curvature_power = bpy.props.FloatProperty(
        name="Contraste",
        default=1.0,
        min=0.1,
        max=5.0
    )
    
    bpy.types.Scene.curvature_min = bpy.props.FloatProperty(default=0.0)
    bpy.types.Scene.curvature_max = bpy.props.FloatProperty(default=0.0)


def unregister_properties():
    del bpy.types.Scene.curvature_scale
    del bpy.types.Scene.curvature_power
    del bpy.types.Scene.curvature_min
    del bpy.types.Scene.curvature_max


classes = (
    MESH_OT_calculate_curvature,
    MESH_OT_clear_curvature,
    VIEW3D_PT_curvature_analyzer,
    VIEW3D_PT_curvature_colorbar,
)


def register():
    for cls in classes:
        bpy.utils.register_class(cls)
    register_properties()


def unregister():
    for cls in reversed(classes):
        bpy.utils.unregister_class(cls)

    if "curvature_icons" in preview_collections:
        bpy.utils.previews.remove(preview_collections["curvature_icons"])
        del preview_collections["curvature_icons"]

    unregister_properties()


if __name__ == "__main__":
    register()
