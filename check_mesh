# -*- coding: utf-8 -*-
import maya.cmds as cmds
import maya.api.OpenMaya as om


def find_triangle_edge(mesh_check):
    """
    check triangle edge
    :param str mesh_check: object long name eg.'|group3|pSphere1'
    :return: Component list
    :rtype: list
    """
    mesh_list = om.MSelectionList()
    mesh_list.add(mesh_check)

    dag_path = mesh_list.getDagPath(0)

    mfn_mesh = om.MFnMesh(dag_path)
    face_numbers = mfn_mesh.numPolygons

    triangle_face_list = ['{0}.f[{1}]'.format(cmds.listRelatives(mesh_check, p=1)[0], a)
                          for a in xrange(face_numbers) if mfn_mesh.polygonVertexCount(a) < 4]

    return triangle_face_list


def find_many_edge(mesh_check):
    """
    Check faces larger than 4 sides
    :param str mesh_check: object long name eg.'|group3|pSphere1'
    :return: Component list
    :rtype: list
    """
    mesh_list = om.MSelectionList()
    mesh_list.add(mesh_check)

    dag_path = mesh_list.getDagPath(0)

    mfn_mesh = om.MFnMesh(dag_path)
    face_numbers = mfn_mesh.numPolygons

    triangle_face_list = ['{0}.f[{1}]'.format(cmds.listRelatives(mesh_check, p=1)[0], a) for a in xrange(face_numbers) if
                          mfn_mesh.polygonVertexCount(a) >= 5]

    return triangle_face_list


def find_bivalent_faces(mesh_check):
    """
    Check bivalent faces
    :param str mesh_check: object long name eg.'|group3|pSphere1'
    :return: vertex index
    :rtype: list
    """
    mesh_list = om.MSelectionList()
    mesh_list.add(mesh_check)
    dag_path = mesh_list.getDagPath(0)

    vertex_it = om.MItMeshVertex(dag_path)
    vertex_indices = []

    while not vertex_it.isDone():
        connect_faces = vertex_it.getConnectedFaces()
        connect_edges = vertex_it.getConnectedEdges()

        if len(connect_faces) == 2 and len(connect_edges) == 2:
            vertex_indices.append(vertex_it.index())
        vertex_it.next()

    return vertex_indices


def find_zero_area_faces(mesh_check, max_face_area):
    """
    Check zero area faces
    :param str mesh_check: object long name eg.'|group3|pSphere1'
    :param float max_face_area: max face area
    :return: face index
    :rtype: list
    """
    mesh_list = om.MSelectionList()
    mesh_list.add(mesh_check)
    dag_path = mesh_list.getDagPath(0)

    poly_it = om.MItMeshPolygon(dag_path)
    poly_indices = []

    while not poly_it.isDone():

        if poly_it.getArea() < max_face_area:
            poly_indices.append(poly_it.index())
        poly_it.next(1)

    return poly_indices


def find_mesh_border_edges(mesh_check):
    """
    Check mesh border edges
    :param str mesh_check: object long name eg.'|group3|pSphere1'
    :return: edge index
    :rtype: list
    """
    mesh_list = om.MSelectionList()
    mesh_list.add(mesh_check)
    dag_path = mesh_list.getDagPath(0)

    edge_it = om.MItMeshEdge(dag_path)

    edge_indices = []

    while not edge_it.isDone():
        if edge_it.onBoundary():
            edge_indices.append(edge_it.index())
        edge_it.next()
    return edge_indices


def find_crease_edges(mesh_check):
    """
    Check mesh border edges
    :param str mesh_check: object long name eg.'|group3|pSphere1'
    :return: edge index
    :rtype: list
    """
    mesh_list = om.MSelectionList()
    mesh_list.add(mesh_check)

    dag_path = mesh_list.getDagPath(0)
    mfn_mesh = om.MFnMesh(dag_path)

    edge_ids, crease_data = mfn_mesh.getCreaseEdges()

    edge_indices = []

    for index in xrange(len(edge_ids)):
        if crease_data[index] != 0:
            edge_indices.append(edge_ids[index])
    return edge_indices


def find_zero_length_edges(mesh_check, min_edge_length):
    """
    Check mesh border edges
    :param str mesh_check: object long name eg.'|group3|pSphere1'
    :param float min_edge_length: min edge length
    :return: edge index
    :rtype: list
    """
    mesh_list = om.MSelectionList()
    mesh_list.add(mesh_check)
    dag_path = mesh_list.getDagPath(0)

    edge_it = om.MItMeshEdge(dag_path)

    edge_indices = []

    while not edge_it.isDone():
        if edge_it.length() < min_edge_length:
            edge_indices.append(edge_it.index())
        edge_it.next()
    return edge_indices


def uv_face_cross_quadrant(mesh_check):
    """
    Check uv face cross quadrant
    :param str mesh_check: object long name eg.'|group3|pSphere1'
    :return: face index
    :rtype: list
    """
    mesh_list = om.MSelectionList()
    mesh_list.add(mesh_check)
    dag_path = mesh_list.getDagPath(0)
    uv_face_list = []

    face_it = om.MItMeshPolygon(dag_path)

    while not face_it.isDone():
        u_quadrant = None
        v_quadrant = None
        uvs = face_it.getUVs()

        for index, uv_coordinates in enumerate(uvs):
            # u
            if index == 0:
                for u_coordinate in uv_coordinates:
                    if u_quadrant is None:
                        u_quadrant = int(u_coordinate)
                    if u_quadrant != int(u_coordinate):
                        component_name = '{0}.f[{1}]'.format(mesh_check, face_it.index())
                        if component_name not in uv_face_list:
                            uv_face_list.append(component_name)
                        print(index, uv_coordinates)
            # v
            if index == 1:
                for v_coordinate in uv_coordinates:
                    if v_quadrant is None:
                        v_quadrant = int(v_coordinate)
                    if v_quadrant != int(v_coordinate):
                        component_name = '{0}.f[{1}]'.format(mesh_check, face_it.index())
                        if component_name not in uv_face_list:
                            uv_face_list.append(component_name)
                        print(index, uv_coordinates)

        face_it.next(None)
    return uv_face_list


def find_double_faces(mesh_check):
    """
    Check all points common to both faces
    :param str mesh_check: object long name eg.'|group3|pSphere1'
    :return: vertex index
    :rtype: list
    """
    mesh_list = om.MSelectionList()
    mesh_list.add(mesh_check)
    dag_path = mesh_list.getDagPath(0)

    vertex_it = om.MItMeshVertex(dag_path)
    vertex_indices = []

    face_id = []

    while not vertex_it.isDone():
        connect_faces = vertex_it.getConnectedFaces()
        connect_edges = vertex_it.getConnectedEdges()
        # print connect_faces, connect_edges
        if len(connect_faces) == 5 and len(connect_edges) == 4:

            vertex_indices.append(vertex_it.index())
            if not face_id:
                face_id = list(connect_faces)
            else:
                face_id = list(set(face_id).intersection(set(list(connect_faces))))
            print(face_id)
        vertex_it.next()
    cmds.select(['{0}.f[{1}]'.format(mesh_check, a) for a in face_id])


if __name__ == '__main__':
    pass
